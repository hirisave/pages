---
title: "Processes and ports: list what’s running, kill by port"
date: "2025-02-13"
tags:
  - shell
  - unix
  - mac
---

Useful commands to see which process is using a port and to free it.

---

### See what’s using a port

**macOS / Linux (lsof):**

```bash
# Process listening on port 8080
lsof -i :8080

# Same, alternative syntax
lsof -i TCP:8080
```

Example output:

```
COMMAND   PID USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
java    12345  you   42u  IPv6 0x...      0t0  TCP *:8080 (LISTEN)
```

The **PID** is what you need to kill.

---

### Get only the PID

```bash
# Just the PID for port 8080 (handy for scripts)
lsof -t -i :8080
```

---

### Kill the process on a port

**One-liner (macOS / Linux):**

```bash
# Kill whatever is on 8080 (sends SIGTERM)
kill $(lsof -t -i :8080)
```

**Multiple ports (one-liner):**

```bash
# Kill whatever is on 8080 and 3000 (sends SIGTERM)
kill $(lsof -t -i :8080 -i :3000) 2>/dev/null

# Same, comma-separated ports (shorter)
kill $(lsof -ti :3000,8080) 2>/dev/null
```

Add more `-i :PORT` for more ports, or use a comma-separated list with one `-i :port1,port2`. `2>/dev/null` hides errors when nothing is listening on a port.

**Force kill if it doesn’t exit:**

```bash
kill -9 $(lsof -t -i :8080)

# Multiple ports
kill -9 $(lsof -ti :3000,8080) 2>/dev/null
```

`-9` is SIGKILL (no graceful shutdown).

---

### Kill multiple ports at once

Use the one-liner above, or a loop when you have many ports:

```bash
# Free 8080 and 3000 (same as one-liner)
for p in 8080 3000; do kill $(lsof -t -i :$p) 2>/dev/null; done
```

---

### List your listening ports

```bash
# All listening TCP ports
lsof -i -P -n | grep LISTEN

# Only your user’s processes
lsof -i -P -n | grep LISTEN | grep $USER
```

`-P` shows port numbers (e.g. `8080` instead of `http-alt`), `-n` avoids DNS lookups.

**With netstat (often available on Linux):**

```bash
netstat -tlnp   # TCP, listening, with PID (Linux)
netstat -tln    # macOS (no -p)
```

---

### Quick reference

| Goal                    | Command |
|-------------------------|--------|
| What’s on port 8080?    | `lsof -i :8080` |
| PID only                | `lsof -t -i :8080` |
| Kill port 8080          | `kill $(lsof -t -i :8080)` |
| Kill multiple ports     | `kill $(lsof -ti :3000,8080) 2>/dev/null` |
| Force kill              | `kill -9 $(lsof -t -i :8080)` |
| All listening (your user) | `lsof -i -P -n \| grep LISTEN \| grep $USER` |
