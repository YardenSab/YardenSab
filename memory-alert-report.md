# Memory alert investigation — 2026-11-18

## Result

The alert could not be investigated from the cloud session: it describes a different machine from the one the session runs on, and that machine was not reachable. Nothing was killed or restarted.

## What the cloud container showed

- **Memory:** 16 GB total, 15.6 GB available, no swap configured at all, so "swap at 97%" cannot be this machine.
- **Uptime:** about one minute; the only sizeable process was the session's own `claude` at roughly 310 MB.
- **`devctl.sh`:** not present anywhere in the container. The repo checkout has no tracked files and no mention of `devctl` or LINDA.
- **Processes from the alert:** none present (no `claude.exe`, no `2.1.325-1ef04b3`).

The `claude.exe` entry suggests the alert came from the user's own computer, likely Windows or WSL. No folder on that computer was shared with the session.

## Read of the alert text alone (not verified)

- **Likely holders:** the `2.1.325-1ef04b3` names look like Claude Code version binaries, so all five listed holders (claude 1553 MB, three at 441/340/321 MB, claude.exe 307 MB) are probably Claude sessions, about 3 GB together.
- **The gap:** 3 GB does not explain nearly full swap. Either something outside the top five holds the rest, or the swap is stale pages left from an earlier spike.
- **Urgency:** with 8608 MB still available, the machine is not at immediate risk. Swap at 97% matters mainly if available memory drops too.

## Next step

Run this on the affected machine and send back the output:

```
./devctl.sh doctor
free -m
ps -eo pid,rss,etime,args --sort=-rss | head -20
```

Alternatively, share the project folder in the Claude desktop app on that computer (Settings → Claude Code → Remote Control) so the session can attach and run the checks directly.
