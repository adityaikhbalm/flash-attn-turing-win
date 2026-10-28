# flash-attn-turing-win

Builds a Windows wheel for [ssiu/flash-attention-turing](https://github.com/ssiu/flash-attention-turing).

Upstream source is not vendored. The workflow fetches it at a pinned commit,
builds on `windows-2022` with Python 3.13 and `torch==2.8.0+cu128`, and uploads
the wheel as a build artifact.

## Build

Actions -> `build-windows-wheel` -> Run workflow.

The first run takes 30-60 minutes: it downloads the CUDA 12.8 toolkit, installs
torch, and compiles cutlass plus the flash-attention kernels.

## Install

Download the artifact, then:

```powershell
pip install flash_attn_turing-0.0.0-cp313-cp313-win_amd64.whl
```

Or from the CLI:

```powershell
gh run download -n flash-attn-turing-win-cp313 -D wheel
```

## Constraints

- cp313 / win_amd64, built against `torch 2.8.0+cu128`. Another Python or torch
  version needs a rebuild, because the wheel contains a compiled `.pyd`.
- The extension does not validate dtype. Pass `float16`, contiguous tensors in
  `(batch, seqlen, nheads, headdim)` layout. Casting and transposing is the
  caller's job.
- Compiled for `sm_75` (Turing). Other architectures need their own build.

Pinned upstream commit: `9ef98fcb506bb1e2fe3cece50935e2935bf6b124`
