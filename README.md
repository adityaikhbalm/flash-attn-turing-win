# flash-attn-turing-win

Builder repo: menghasilkan **wheel FlashAttention-Turing untuk Windows** lewat GitHub Actions.

- Upstream: [`ssiu/flash-attention-turing`](https://github.com/ssiu/flash-attention-turing)
- Commit di-pin: `9ef98fcb506bb1e2fe3cece50935e2935bf6b124`
- Runner: `windows-2022` (MSVC + CUDA 12.8)
- Target: Python 3.13 · `torch==2.8.0+cu128` · `win_amd64` · `sm_75`

Repo ini **tidak** menyimpan source upstream. Saat build, workflow meng-checkout
upstream pada commit yang di-pin (termasuk submodule `csrc/cutlass`).

## Kenapa Windows runner

Wheel berisi ekstensi native (`.pyd`). Build di Linux menghasilkan `.so` yang
tidak bisa dipakai di Python Windows. Karena itu runner harus Windows dan
Python/torch harus sama persis dengan environment target.

## Cara pakai

1. Buka tab **Actions** → `build-windows-wheel` → **Run workflow**, atau push ke `main`.
2. Setelah selesai, unduh artifact `flash-attn-turing-win-cp313`.
3. Install di environment target:

```powershell
pip install <path>\flash_attn_turing-0.0.0-cp313-cp313-win_amd64.whl
```

Atau lewat CLI:

```powershell
gh run download --repo <owner>/flash-attn-turing-win -n flash-attn-turing-win-cp313 -D .\wheel
```

## Catatan penting

- Wheel **hanya** cocok untuk Python 3.13 + torch 2.8.0+cu128. Ganti salah satu → build ulang.
- Ekstensi hasil build **tidak memvalidasi dtype**. Semua tensor yang masuk harus
  `float16` (bukan bf16) dan contiguous. Cast/transpose dilakukan di adapter
  pemanggil, bukan di repo ini.
- Layout API: `q, k, v` = `(batch, seqlen, nheads, headdim)`.
- Build pertama bisa 30–60 menit (unduh CUDA + kompilasi cutlass).
