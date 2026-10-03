# Remote environment inspection

Inspection date: 2026-10-02. Read-only inspection over SSH; no software installed,
no training started, no model downloaded. The instance runs without a GPU to
reduce rental cost. This snapshot applies to the inspected instance only.

## Current image reinspection

Reconnected after the user changed the image. SSH public-key authentication still
works. This section supersedes the previous image snapshot for current setup.
No remote files or packages were modified by this inspection.

| Item | Current result |
| --- | --- |
| Host / OS | Same hostname; Ubuntu 22.04.5 LTS |
| Conda | Only base environment; Python 3.12.3 |
| PyTorch / torchvision | 2.8.0+cu128 / 0.23.0+cu128; torch imports successfully |
| CUDA runtime | PyTorch reports 12.8; CUDA device not visible |
| nvidia-smi | Absent from current no-GPU image; GPU-mode availability not tested |
| LeRobot / Transformers / HF Hub / Accelerate / PyAV | Not installed in base |
| grpcio | 1.74.0 |
| System disk | Quota view: 30 GB total, 52 MB used, approximately 30 GB free |
| Data disk | 50 GB total, 44 KB used, approximately 50 GB free |
| Container quota | 2 GiB RAM, 0.5 CPU; unchanged in no-GPU mode |
| Source checkout | Neither `/root/lerobot_pi` nor its prior checkout exists |
| Data project root | `/root/autodl-tmp/pi05_ros` absent |
| Tools | git, rsync, curl available; Python/Conda absent from noninteractive default PATH |
| Connectivity | GitHub/PyPI/mirror homepage HTTP 200; official HF TLS connection reset |

Next: restore the source checkout from GitHub; prepare an isolated Python 3.12
LeRobot environment; configure caches/data/output on the data disk. Preserve the
working torch 2.8 CUDA 12.8 baseline if dependency resolution permits. Do not
validate GPU functionality in no-GPU mode. Git clone/pull and model/tokenizer
file downloads remain separate checks. The displayed system-disk usage is the
container quota view and does not include all preinstalled image-layer files.

## Previous image inspection (historical)

| Item | Result |
| --- | --- |
| Hostname | `autodl-container-85bd408359-d1d530f5` |
| User / OS / architecture | root / Ubuntu 22.04.5 LTS / x86_64 |
| GPU | Not visible, expected in no-GPU mode; earlier A800 allocation remains unverified for this instance |
| Noninteractive shell | Python/Conda absent from default PATH; explicit interpreter paths work |
| Conda base | `/root/miniconda3`, Python 3.12.3 |
| Existing lerobot environment | `/root/miniconda3/envs/lerobot`, Python 3.10.12 |
| Existing LeRobot / Transformers | 0.4.3 / 4.53.3; installed metadata requires Python >=3.10 |
| Selected LeRobot version | PyPI metadata for 0.6.1 requires Python >=3.12 |
| Confirmed cloned repository | `/root/lerobot_pi/robotic_manipulation_practice_remote`, commit `33088a6`, clean working tree |
| Existing torch / torchvision | 2.7.1+cu128 / 0.22.1+cu128 in lerobot env; torch imports successfully |
| Existing environment consistency | `pip check`: No broken requirements found |
| pi05 and async server files | Expected `policies/pi05` and `async_inference/policy_server.py` absent from installed 0.4.3 package |
| Base torch / torchvision | 2.7.0+cu128 / 0.22.0+cu128 |
| CUDA Toolkit | `/usr/local/cuda/bin/nvcc`, 12.8.93 |
| Container memory limit | cgroup v1: 2147483648 bytes = 2 GiB |
| Container CPU quota | 50000/100000 = 0.5 CPU; `nproc=112` does not represent quota |
| Shared memory | `/dev/shm`: 2 GiB |
| System disk | 30 GB total, approximately 11 GB free |
| Data disk | `/root/autodl-tmp`: 50 GB total, approximately 50 GB free, XFS quota mount |
| `/workspace` | Does not exist |
| Tools | git 2.34.1, ssh, rsync, curl, wget available; tmux/uv absent from default PATH |
| GitHub HTTPS homepage | HTTP 200 |
| GitHub Git transport | `git ls-remote` did not complete within 12-second timeout; clone/pull not yet validated |
| PyPI | HTTP 200 |
| Hugging Face official endpoint | TLS connection reset, including model metadata API |
| hf-mirror.com | HTTP 200; `api/models/lerobot/pi05_base` HTTP 200; weight download and gated tokenizer not tested |

`free -h` reports host memory (about 1 TiB); the cgroup allocation is the useful
limit for this container. GPU-mode CPU/RAM quotas must be checked again after
switching modes.

## Implications and next preparation

1. Keep the existing 0.4.3 environment intact. Prepare a separate Python 3.12
   environment for the locally selected LeRobot 0.6.1 version. Do not interpret
   a clean pip check as compatibility with the intended pi05 pipeline.
2. Use the confirmed checkout `/root/lerobot_pi/robotic_manipulation_practice_remote`
   for source code. Keep HF/model caches, datasets and training outputs on the
   separate data disk under `/root/autodl-tmp/pi05_ros`; confirm persistence and
   quota before placing important artifacts there.
3. Budget disk space before downloading weights or training: include environment,
   model cache, dataset, optimizer state and multiple checkpoints. The 50 GB data
   disk should not be assumed sufficient for the full pipeline. Avoid duplicate
   model downloads; back up artifacts locally before releasing the instance.
4. The user has cloned the project repository successfully. Inspection confirms
   its local Git root and clean checkout; this inspection has not independently
   tested a subsequent fetch/pull. The earlier upstream ls-remote timeout remains
   a separate observation.
5. Use no-GPU mode for small source/config operations and dependency checks.
   Actual model loading, gradients, CUDA checks and inference require GPU mode
   and reinspection of memory/CPU quotas.
6. HTTP 200 from a mirror proves metadata reachability only. Validate exact
   pinned model/tokenizer access and integrity separately; no credentials have
   been read or transmitted by this inspection.

No OpenAI API key is required on the server. Remote installation, repository
clone and model download have not been performed.
