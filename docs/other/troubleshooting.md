# Troubleshooting

## No ALB window? 
- Check that EuroScope has loaded `ALB.dll`.
- Confirm that `ALBCore.dll` is present in the same plugin folder.

- The window may stay hidden until ```.alb open```

## Messages not sending? 
- Verify FMR claimed and network status.

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



