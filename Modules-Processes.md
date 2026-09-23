# Processes

> Print number of running processes and threads

| | |
|---|---|
| Module type | `processes` |
| Default order | 13 (only used by `--gen-config`) |
| Module source | `src/modules/processes/processes.c` |
| Detection source | `src/detection/processes/` |

Prints the number of processes and the total number of threads across them, as
`<processes> (<threads> threads)`.

```
Processes: 869 (3476 threads)
```

## Platform support

| Platform | Implementation | Notes |
|---|---|---|
| Linux | `processes_linux.c` | Parses `/proc/<pid>/stat` for every numeric entry of `/proc` |
| Android | `processes_linux.c` | Same reader, subject to `/proc` restrictions |
| FreeBSD | `processes_bsd.c` | `sysctl({CTL_KERN, KERN_PROC, KERN_PROC_PROC})` |
| DragonFly | `processes_dbsd.c` | `KERN_PROC_ALL`, optionally with `KERN_PROC_FLAG_LWKT` |
| NetBSD | `processes_nbsd.c` | `KERN_PROC2` plus a `KERN_LWP` call per process |
| OpenBSD | `processes_obsd.c` | `KERN_PROC_ALL \| KERN_PROC_SHOW_THREADS`, counting thread entries |
| macOS | `processes_apple.c` | `KERN_PROC_ALL`, then one `proc_pidinfo()` call per process |
| Solaris / illumos | `processes_sunos.c` | `/proc/<pid>/status` for the flags, `/proc/<pid>/psinfo` for the counts |
| Haiku | `processes_haiku.c` | `get_system_info()` |
| GNU/Hurd | `processes_gnu.c` | `libps` process list |
| Windows | `processes_windows.c` | `SystemHandleCountInformation`, or a `SystemProcessInformation` walk |

`src/detection/processes/processes_nosupport.c` exists in the tree but is **not listed in
`CMakeLists.txt`**, so it is never compiled — the module has a real implementation everywhere.

## Configuration

| Key | Type | Default | Description |
|---|---|---|---|
| `countKprocs` | boolean | `false` | Include kernel processes and their threads in the count. |
| `key` | string | module name | Module key. A single space hides the key and the separator. |
| `keyColor` | color | – | Overrides `display.color.keys`. |
| `keyWidth` | integer | – | Overrides `display.key.width`. |
| `keyIcon` | string | built-in glyph | Printed when `display.key.type` includes the icon bit. Any glyph works, `""` prints none. |
| `outputColor` | color | – | Overrides `display.color.output`. |
| `format` | string | – | Custom output format (see below). |
| `condition` | object | – | Show the module only if the conditions match. |

`countKprocs` means different things per platform, because "kernel process" is a platform
concept: on Linux it is `PF_KTHREAD` in `/proc/<pid>/stat`, on the BSDs a `P_KPROC`/`P_SYSTEM`
flag, on Solaris `PR_ISSYS`, on the Hurd pid 2, on Haiku the kernel team, and on Windows the
entries with parent PID 0.

## Format string

Run `fastfetch -h processes-format` for the authoritative list.

| Variable | Description |
|---|---|
| `{result}` | Process count |
| `{threads}` | Thread count |

Both halves of the default output are therefore reachable from a custom `format`; see the
`{result}|{threads}` example below.

## JSON output

```jsonc
{
    "type": "Processes",
    "result": {
        "processes": 869,
        "threads": 3481
    }
}
```

## Examples

```jsonc
{ "type": "processes", "key": "Tasks", "format": "{result}" }
```

```jsonc
// Include kernel processes
{ "type": "processes", "countKprocs": true }
```

```jsonc
// Reproduce the default output shape through the format string
{ "type": "processes", "format": "{result} ({threads} threads)" }
```

## Pitfalls

- **The thread count is a separate variable, not part of `{result}`.** `{result}` is the process
  count alone, so a custom `format` that wants the default output's shape has to write
  `{result} ({threads} threads)` itself.
- **`countKprocs` is a no-op on macOS.** `KERN_PROC_ALL` does not return kernel-only entries on
  current macOS, so the `P_SYSTEM` filter never matches: measured counts are identical with the
  option on and off (879–882 processes in both cases).
- **The macOS thread count is incomplete for system processes.** Threads come from
  `proc_pidinfo(PROC_PIDTASKINFO)`, which fails for system processes — the source comment says so
  explicitly. Those processes are counted, but their threads are not.
- **The numbers are a snapshot, not a stable value.** Three consecutive runs on an idle machine
  produced 882/3593, 882/3598 and 881/3598. Scripts that compare the value against a stored one
  will see spurious differences.
- **macOS performs one syscall per process.** Thread counting needs a separate
  `proc_pidinfo()` call for each of the ~880 entries, which makes this module noticeably more
  expensive on macOS than on Linux, where a single `/proc/<pid>/stat` read per process yields
  both numbers.
- **The BSD/macOS readers retry with a larger buffer.** The buffer size comes from a first
  `sysctl()` call and is then grown by one eighth plus one entry before the data is fetched. If
  the process table still grows past that between the two calls, the second `sysctl()` fails and
  the module prints a `sysctl(...) failed` error instead of a count.
- **OpenBSD's error messages always name `KERN_PROC_ALL`**, even when `countKprocs` selected
  `KERN_PROC_KTHREAD` — the two failure paths share a literal string, so the message does not
  describe what was actually requested.
- **`/proc` may be unavailable or restricted.** On Linux and Solaris the reader starts with
  `opendir("/proc")` and reports `opendir("/proc") failed` when it is missing, which is what
  happens in some container and hardened-kernel configurations.
- **`countKprocs` on Windows changes the source of the number.** With it enabled and a recent
  Windows build, the counts come from `SystemHandleCountInformation`, which reports the totals
  directly; otherwise the module walks `SystemProcessInformation` and filters out the entries
  whose `InheritedFromUniqueProcessId` is `0` (the Idle process, and in practice the System
  process). The two paths can therefore disagree slightly.

## Implementation

### Linux and Android

`ffDetectProcesses()` opens `/proc`, takes every entry whose name starts with a digit and whose
`d_type` is a directory or unknown, and reads `/proc/<pid>/stat` into a 512-byte buffer. The
process name is skipped with `memrchr(buffer, ')', length)`, so a name containing `)` or spaces
does not shift the field positions.

The remaining fields are then walked positionally from field 3 up to field 20, which is
`num_threads`:

- **field 9** (`flags`) is tested against `PF_KTHREAD` (`0x00200000`, redefined locally to avoid
  a kernel-header dependency); a match skips the process unless `countKprocs` is set.
- **field 20** supplies `threads`.
- every other field is skipped by advancing to the next space.

Both the field walk and the initial `memrchr()` are bounds-checked against the number of bytes
actually read; a truncated line skips that process rather than reading past the buffer.

### FreeBSD and DragonFly

Two separate files exist because the CMake FreeBSD block splits on `DragonFly`:

- **FreeBSD** uses `KERN_PROC_PROC`, which returns one entry per process, and skips entries with
  the `P_KPROC` flag unless `countKprocs` is set. Threads come from `ki_numthreads`.
- **DragonFly** uses `KERN_PROC_ALL`, adding `KERN_PROC_FLAG_LWKT` when `countKprocs` is set.
  Entries with `kp_pid == -1` are kernel threads: they add one thread each and are not counted
  as processes, while everything else contributes one process and `kp_nthreads` threads.

### NetBSD

`KERN_PROC2` with `KERN_PROC_ALL` fetches the process list. Thread counting is a second call per
process — `{CTL_KERN, KERN_LWP, pid, sizeof(struct kinfo_lwp), 0}` — whose returned length is
divided by `sizeof(struct kinfo_lwp)`. Because a skipped (`P_SYSTEM`) process is `continue`d
before that call, the threads of system processes are excluded along with the processes
themselves.

### OpenBSD

The request uses `KERN_PROC_SHOW_THREADS`, so the result contains one entry per *thread*. The
loop therefore increments `threads` unconditionally and increments `processes` only when
`p_tid == -1`, which marks the thread that represents the process itself. `countKprocs` swaps
`KERN_PROC_ALL` for `KERN_PROC_KTHREAD`. The count argument must be non-zero for the data fetch,
so it is set to `length / sizeof(struct kinfo_proc)` before the second call.

### macOS

`KERN_PROC_ALL` returns `struct kinfo_proc` entries. `countKprocs` controls whether entries with
`P_SYSTEM` in `kp_proc.p_flag` are skipped. For each remaining entry a
`proc_pidinfo(pid, PROC_PIDTASKINFO, …)` call supplies `pti_threadnum`; a failed call contributes
no threads but does not stop the loop.

### Solaris

`/proc` is walked like on Linux, but the data comes from two different files: `/proc/<pid>/status`
(a `pstatus_t`) is read first and its `pr_flags` tested against `PR_ISSYS`, then
`/proc/<pid>/psinfo` (a `psinfo_t`) supplies `pr_nlwp` as the thread count. A process that has
exited between the two reads simply does not contribute.

### Haiku

`get_system_info()` reports `used_teams` and `used_threads` directly. With `countKprocs` unset,
the kernel team is then subtracted by calling `get_team_info(B_SYSTEM_TEAM, …)` and removing one
from `processes` and `ti.thread_count` from `threads`.

### GNU/Hurd

`libps` is used through `ps_context_create(getproc(), …)` and
`proc_stat_list_add_all()`, with `PSTAT_NUM_THREADS` requested as a per-process flag so that
`proc_stat_num_threads()` is valid. The kernel task is identified as pid 2 and skipped unless
`countKprocs` is set. Both the list and the context are freed on every exit path, including the
error paths.

### Windows

Two strategies, chosen by `countKprocs` and by whether the smaller information class exists
(`ffIsSystemBasicProcessInfoAvailable()`):

1. **`SystemHandleCountInformation`** returns `ProcessCount` and `ThreadCount` directly, so no
   table walk is needed. The struct is zero-initialised because the kernel only fills the lower
   32 bits of each counter.
2. **`SystemProcessInformation`** is walked as a linked list via `NextEntryOffset`, summing
   `NumberOfThreads` per entry. The buffer size is discovered by calling with `size == 0`, and
   `STATUS_INFO_LENGTH_MISMATCH` is retried up to four times with the buffer grown by five
   entries each time.

### Shared

`FFProcessesResult` holds two `uint32_t` counters. Note that `src/detection/processes/processes.h`
includes `modules/processes/option.h`: the detection layer takes the module's own options struct
(`const FFProcessesOptions*`) rather than a private detection struct, so `countKprocs` is read
straight from the user configuration. `ffPrintProcesses()` and `ffGenerateProcessesJsonResult()`
each call the detector themselves; a run uses one or the other, never both.
