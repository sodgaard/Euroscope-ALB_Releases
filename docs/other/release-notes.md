# Release Notes

For detailed asset-by-asset history, use the GitHub releases page:

- [Euroscope-ALB_Releases Releases](https://github.com/sodgaard/Euroscope-ALB_Releases/releases){target="_blank" rel="noopener"}

## Current release line

The current release pointer in this repository is:

- `0.3.0`

This release line reflects the current public packaging model:

- `ALB.dll` is the EuroScope-facing loader
- `ALBCore.dll` is the runtime plugin
- EuroScope should load `ALB.dll`
- the loader may update `ALBCore.dll`
- the loader may refresh `alb-config.default.json`
- the loader must not overwrite live `alb-config.json`

## What changed in the current packaging model

Compared with older ALB installs, the most important public-facing changes are:

- ALB now uses a loader/core split instead of a single DLL expectation
- install guidance is based on `ALB.dll` plus `ALBCore.dll`
- release/update behavior is tied to the release repo and its version pointer
- the live operational config is preserved during loader updates

## Documentation update: backend seqsync load-management

Updated June 15, 2026.

The current documentation now reflects backend sequence synchronization
load-management through Stage 4.1:

- added `throttled`, `horizon`, and `suspend` seqsync mode coverage alongside the default `normal` mode
- documented bounded recovery when returning to `normal`
- documented `.alb seqsync status` and seqsync mode commands
- documented `backendSeqSync` config knobs and clamps
- documented PERF summary lines for queueing, horizon suppression, suspend behavior, and bounded normal recovery

Important boundaries remain unchanged:

- canonical sequence state stays backend-only
- no peer-owned resequencing was introduced
- `EAT:AR` and `EAT:LT` algorithms are unchanged by seqsync load-management
- Stage 5 FLOAT or advisory protocol is not implemented

Implementation build validation succeeded in the implementation thread. Live
EuroScope runtime or performance validation is still pending.

## Notes for users

If you are updating from an older ALB installation:

- make sure EuroScope loads `ALB.dll`, not `ALBCore.dll`
- keep `alb-config.json` as your live config
- treat `alb-config.default.json` as a template/default file

For installation details, see [Download & Install](../download-install.md).
