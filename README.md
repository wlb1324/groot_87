# GR00T N1.7 — 87-episode training pipeline overlay

This public repository contains the GR00T N1.7 source files changed for the 87-episode pressure/proprioception fine-tuning experiments. It is a **source overlay**, not a complete GR00T checkout: apply these files to a compatible NVIDIA Isaac-GR00T checkout before running. The source directory used for this export had no Git metadata, so its exact upstream commit/release could not be established; this repository therefore does not claim to be a line-by-line diff against a particular upstream revision.

## What is included

- N1.7 model/config and state-action processing changes for the experiment inputs.
- Three experiment launchers corresponding to the recorded input-history variants below.
- Two generic local fine-tuning launchers used by the same pipeline.
- Data types and state-action processor files required by those changes.

No dataset, selected-episode manifest, checkpoint/model weights, run logs, credentials, or environment dump is included. The exact 87 episode IDs and the original shell commands are not packaged here; supply the intended dataset and training arguments through the GR00T fine-tuning CLI/configuration.

## Recorded input variants

| Launcher | State input described by the code |
|---|---|
| `launch_finetune_action_head_teacher.py` | Five-frame history of `[L2, R2, left arm (7), right arm (7), left gripper, right gripper]` (18 values per frame). |
| `launch_finetune_action_head_current_pressure.py` | Current-frame `L2/R2` followed by five-frame history of the 14 arm-joint values and current two gripper values; the code reports 74 active values. |
| `launch_finetune_action_head_current_pressure_hist5_proprio.py` | Current-frame `L2/R2` followed by five frames of 16D proprioception (14 arm-joint + 2 gripper values); 82 active values. |

For all three launchers, the state modality is checked against the expected field order and five-frame delta indices `[-4, -3, -2, -1, 0]`; history padding is enabled to avoid wrapping episode-start frames to the episode tail. The launcher source also configures three camera sizes (head target 256×256; left/right wrist targets 224×224) and uses the inherited GR00T language/vision processing path. Refer to the code for exact implementation details.

The first variant is the five-frame state-history baseline; the second represents current pressure with joint-history input; the third represents current pressure with five-frame full proprioception history. These names describe input packing, not separate model architectures.

## Portability

Five launcher copies in this repository were adjusted to resolve the pretrained model path as:

```python
os.environ.get("GROOT_MODEL_NAME", config.model.model_name)
```

This removes the original server-specific model path from the public export. Set `GROOT_MODEL_NAME` to a local model directory or a supported model identifier when launching. Dataset path, output directory, batch size, learning rate, number of steps/epochs, worker count, and other run-specific values are supplied through `FinetuneConfig` arguments; they are not hard-coded as a universal recipe here.

Example overlay from the root of a compatible GR00T checkout (review before overwriting files):

```powershell
Copy-Item -Recurse -Force .\gr00t\* <GR00T_CHECKOUT>\gr00t\
```

Then run the relevant `gr00t.experiment.launch_finetune_*` module from that checkout with the desired `FinetuneConfig` arguments and environment. Do not assume compatibility with an arbitrary GR00T revision; inspect the upstream diff and validate the data schema/configuration before training.

## Provenance and license

The included NVIDIA source files retain their SPDX copyright and Apache-2.0 notices. `LICENSE` is the upstream Apache License 2.0 text. This repository contains code only; GR00T model weights are not included and remain subject to their own license terms. Upstream project: [NVIDIA Isaac-GR00T](https://github.com/NVIDIA/Isaac-GR00T).

The `GROOT_MODEL_NAME` portability override is an export-side change made for this public repository; it does not modify the training files on the server.
