# Linux Process Management — Hands-on Lab

## Objective

The goal of this lab was to understand Linux processes by actually creating, inspecting, controlling, and terminating processes.

I focused on understanding the output of the commands instead of just memorizing their syntax.

---

## Lab 1 — View Running Processes

Command:

```bash
ps aux | head
```

### What I observed

I looked at the `USER`, `PID`, `%CPU`, `%MEM`, `STAT`, and `COMMAND` columns.

The most important thing I noticed was that every process has its own PID.

---

## Lab 2 — Monitor Processes in Real Time

Command:

```bash
top
```

### What I observed

`top` continuously updates the process list and shows CPU, memory, and load information.

I also checked the load average displayed at the top.

I exited using:

```text
q
```

---

## Lab 3 — Create a Background Process

Command:

```bash
sleep 500 &
```

### What I observed

The shell returned a job number and a PID.

Example:

```text
[1] 12345
```

I learned that the job ID (`1`) and PID (`12345`) are different identifiers.

---

## Lab 4 — Find the Process

Command:

```bash
pgrep sleep
```

I used the returned PID to inspect the process:

```bash
ps -fp <PID>
```

I also checked its PID and PPID:

```bash
ps -o pid,ppid,stat,cmd -p <PID>
```

---

## Lab 5 — Understand the Parent Process

After finding the PPID, I inspected the parent:

```bash
ps -fp <PPID>
```

I used `pstree` to understand the relationship visually:

```bash
pstree -p
```

This helped me understand the parent-child relationship between processes.

---

## Lab 6 — Stop the Process

I terminated the `sleep` process using:

```bash
kill <PID>
```

Then I checked:

```bash
pgrep sleep
```

The process was no longer running.

I learned that `kill` sends a signal to a process and that the default signal is `SIGTERM`.

---

## Lab 7 — Foreground and Background Jobs

I started a foreground process:

```bash
sleep 500
```

Then pressed:

```text
Ctrl + Z
```

I checked the job:

```bash
jobs
```

Then continued it in the background:

```bash
bg
```

Finally, I brought it back to the foreground:

```bash
fg
```

This helped me understand how shell job control works.

---

## Lab 8 — CPU-Heavy Test Process

For a controlled test, I started:

```bash
yes > /dev/null &
```

I checked the process using:

```bash
pgrep yes
```

Then opened:

```bash
top
```

I could see the process consuming CPU resources.

After the test, I stopped it:

```bash
pkill yes
```

This demonstrated how a process consuming excessive CPU can be identified and terminated.

---

## What I Learned

The most important lesson from this lab was that process management is not just about knowing commands.

The important part is understanding the relationship between:

```text
Process
   |
   ├── PID
   ├── PPID
   ├── State
   ├── CPU usage
   ├── Memory usage
   └── Command
```

I can now use basic Linux tools to identify a process, understand its parent, monitor its resource usage, control foreground/background jobs, and terminate it using appropriate signals.
