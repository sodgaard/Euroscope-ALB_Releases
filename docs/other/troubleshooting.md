# Troubleshooting

## No ALB window? 
- Check that EuroScope has loaded `ALB.dll`.
- Confirm that `ALBCore.dll` is present in the same plugin folder.

- The window may stay hidden until ```.alb open```

## Messages not sending? 
- Verify FMR claimed and network status.

## Reload config behavior

Use either:

```text
.alb reload
```

or the `[Reload config]` entry in the `Timelines` menu.

Expected behavior in current builds:

- `alb-config.json` is re-read without needing a full restart
- startup-only defaults such as `eatModeOnStartup`, `etaModeOnStartup`, and
  `holdEatWriteOnStartup` are not supposed to overwrite your active runtime
  session during reload
- active runtime selections such as `EAT`, `ETA`, and `HLW` are preserved more
  cleanly than in older builds
- ALB also tries to preserve the current timeline zoom

If a changed config value does not take effect, confirm that:

- you edited `alb-config.json`, not only `alb-config.default.json`
- the JSON stayed valid
- the setting is not one of the startup-only baseline keys

## Backend seqsync status and control

Show current backend seqsync status with:

```text
.alb seqsync status
```

Useful fields in the status output include:

- current mode
- `recoveryDrain=0/1`
- queue depth
- `set2`, `del`, and `hclr` queue counts
- max queue age
- queued ICAOs where relevant
- peer health summary when enabled, such as `peerHealth`, `peerAvgMs`, `peerMaxMs`, `peerWarnedLocal`, and `peerWarnedPeers`

When ALB is in `suspend`, the status line also shows that canonical TX is
suspended and that operational `DEL` remains allowed and rate-limited.

If passive peer health monitoring is enabled, the status line may also show the
last warned peer and warning age.

If you are working as a non-FMR peer and use runway `Advance 1` or
`Resequence`, ALB may also report that the request was sent, accepted and
waiting for canonical sync, or timed out if the authoritative reply path does
not complete.

Change seqsync mode with:

```text
.alb seqsync normal
.alb seqsync throttled
.alb seqsync horizon
.alb seqsync suspend
```

## Mixed peers and reconnects

Current ALB builds handle backend reconnects and mixed backend or
scratchpad-only peer situations more safely than older ones.

Practical meaning:

- you do not need a separate reconnect command to enable this behavior
- normal FMR ownership, seqsync commands, and fallback behavior remain the
  operator-facing controls
- if transport health changes, use `Peers`, `.alb seqsync status`, and normal
  operational coordination to understand which path is currently in effect

If a peer seems to have fallen behind after a reconnect window:

- check `.alb seqsync status`
- confirm who is the active FMR
- if config was just changed, use `.alb reload` once and let the normal backend
  sync settle

## Seqsync diagnostics

For deeper technical inspection, look for PERF summary lines such as:

- `BACKEND_SEQ_SYNC_SUMMARY`
- `BACKEND_SEQ_TX_THROTTLE`
- `BACKEND_SEQ_HORIZON`
- `BACKEND_SEQ_SUSPEND`
- `BACKEND_SEQ_NORMAL_RECOVERY`

Relevant field families include `backendSeq.txQueue.*`,
`backendSeq.horizon.*`, `backendSeq.suspend.*`,
`backendSeq.normalRecoveryDrain.*`, `backendPeerHealth.*`, and backend poll
timing fields such as `backendPoll.*`.

If you are deliberately validating this behavior in a technical session, use:

```text
.alb perftest peers on 300
```

See [Backend Transport](../technical/backend-transport.md) and
[Playback & Recording](../technical/playback-recording.md) for the technical
interpretation.



