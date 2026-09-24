# GR00T N1.7 RoboTwin inference adapters

This directory contains the local RoboTwin-side adapters and optional GR00T
server utilities used in the GR00T action-scheduling experiments. It is a
source overlay, not a complete installation of Isaac-GR00T or RoboTwin.

## Included

- `groot_robotwin_policy.py`: synchronous RoboTwin policy adapter.
- `groot_robotwin_async_policy.py`: asynchronous inference with an action buffer.
- `groot_robotwin_temporal_policy.py`: temporal-ensemble adapter.
- `groot_robotwin_rtc_policy.py`: RTC-enabled adapter.
- `groot_robotwin_minloop_policy.py`: minimal sync/async/RTC comparison adapter.
- `rtc_gr00t_server.py`: GR00T server wrapper that forwards RTC options to the
  action policy.
- `minloop_timed_server.py` and `measure_backbone_action_head_server.py`:
  optional server-side timing instrumentation.
- `configs/`: one-episode RoboTwin configuration examples. They are templates,
  not claims of task success or a turnkey benchmark.

## Integration assumptions

Use these files alongside compatible checkouts of NVIDIA Isaac-GR00T N1.7 and
RoboTwin. The RoboTwin evaluation launcher must be able to import the adapter
module. Install the dependencies required by those upstream projects; this
directory does not vendor either project or their dependencies.

The client adapters require `GROOT_SERVER_HOST` to be set explicitly. Optional
environment variables include `GROOT_SERVER_PORT` (default `5555`),
`GROOT_EXECUTION_HORIZON` (default `16`), and `GROOT_RUN_DIR` (default
`./runs/groot_robotwin`). RTC client/server ports and parameters are configured
by the corresponding adapter/server arguments or `GROOT_RTC_*` variables; see
the source for the exact names and defaults. Server utilities bind to loopback
by default; pass an explicit host when a remote RoboTwin client must connect.

The policy adapters expect the three camera keys used in the original setup:
head/ego, left wrist, and right wrist (`*_res320x240_freq20`). The task language
and RobotWin end-effector action conversion are defined in the adapter source.
Check the target RoboTwin task's observation/action schema before use; these
experimental adapters are not a safety-rated robot controller.

## Example configs

The YAML files use relative output directories and an example task/seed. Update
the task, checkpoint registration, server address, and output location to match
your local upstream installations before running RoboTwin evaluation.

No model weights, datasets, run logs, videos, server addresses, or machine-local
paths are included.

