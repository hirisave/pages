---
title: "Logs: useful commands, tailing, searching"
date: "2025-02-13"
tags:
  - shell
  - logs
  - unix
---

Handy commands for watching and searching log files from the terminal.

---

### Tailing (watch logs as they’re written)

```bash
# Last 100 lines, then keep streaming
tail -n 100 -f /var/log/app.log

# Same, common shorthand
tail -f /var/log/app.log
```

`-f` follows the file (like `tail -f` in a loop). Use **Ctrl+C** to stop.

---

### Tailing multiple files

```bash
# Stream several logs at once; each line is prefixed with the filename
tail -f /var/log/app.log /var/log/nginx/access.log
```

---

### Searching inside logs

```bash
# Lines that contain "error" (case-sensitive)
grep "error" /var/log/app.log

# Case-insensitive
grep -i "error" /var/log/app.log

# Show 2 lines of context above and below each match
grep -C 2 "NullPointerException" /var/log/app.log

# Only lines that do *not* match (e.g. skip health checks)
grep -v "GET /health" /var/log/access.log
```

---

### Combine tail + grep (live filter)

```bash
# Only see new lines that contain "error"
tail -f /var/log/app.log | grep --line-buffered "error"
```

`--line-buffered` makes matches show up immediately instead of being buffered.

---

### Search in compressed logs

```bash
# .gz files: use zgrep so you don’t have to uncompress first
zgrep "error" /var/log/app.log.1.gz

# Or uncompress on the fly and grep
zcat /var/log/app.log.1.gz | grep "error"
```

---

### Last N lines that match

```bash
# Last 50 lines of file, then grep in that window
tail -n 1000 /var/log/app.log | grep "error" | tail -n 50
```

---

### Count occurrences

```bash
# How many lines contain "error"?
grep -c "error" /var/log/app.log
```

---

### Time range (if logs have timestamps)

When log lines start with a date (e.g. `2025-02-13 10:30:00`), you can narrow by time:

```bash
# Lines from 10:00–11:00 (adjust pattern to your log format)
grep "2025-02-13 10:" /var/log/app.log
```

For more complex ranges, combine with `sed`/`awk` or use a log tool (e.g. `journalctl`, ELK, Grafana Loki).

---

### Quick reference

| Goal              | Command |
|-------------------|--------|
| Follow log        | `tail -f /path/to/log` |
| Last N lines      | `tail -n 200 /path/to/log` |
| Search            | `grep "text" /path/to/log` |
| Search (ignore case) | `grep -i "text" /path/to/log` |
| Context around match | `grep -C 2 "text" /path/to/log` |
| In .gz            | `zgrep "text" /path/to/log.gz` |
| Live filter       | `tail -f log \| grep --line-buffered "text"` |
| Count matches     | `grep -c "text" /path/to/log` |
