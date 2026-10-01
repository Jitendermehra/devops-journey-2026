# Linux Process Management

## What is a Process?

A process is a running instance of a program.

Whenever I start a command or application, Linux creates a process for it and assigns it a unique Process ID (PID).

For example:

```bash
sleep 500 &
```

This starts the `sleep` program as a background process.

Linux then assigns it a PID that I can use to inspect or manage that process.

---

## 1. Understanding `ps`

The `ps` command is used to view running processes.

I used:

```bash
ps aux
```

To get a shorter output:

```bash
ps aux | head
```

Some important columns are:

| Column | Meaning |
|---|---|
| USER | User who owns the process |
| PID | Process ID |
| %CPU | CPU usage of the process |
| %MEM | Memory usage of the process |
| VSZ | Virtual memory used by the process |
| RSS | Physical memory currently used |
| TTY | Terminal associated with the process |
| STAT | Current process state |
| START | Time when the process started |
| TIME | CPU time used by the process |
| COMMAND | Command/program that started the process |

### Why `ps` is useful

When troubleshooting a Linux server, I can use `ps` to find out:

- Which processes are running
- Which user owns a process
- How much CPU or memory a process is using
- What command started the process
- The parent process of a process

---

# 2. PID — Process ID

PID stands for **Process ID**.

Every running process gets a unique number from the operating system.

Example:

```text
PID
12345
```

I can use the PID to inspect or manage a process.

For example:

```bash
ps -fp 12345
```

Or send a signal to it:

```bash
kill 12345
```

### Simple way to remember

> PID is the unique ID of a process.

---

# 3. PPID — Parent Process ID

PPID stands for **Parent Process ID**.

A process can be started by another process. The process that starts it is called the parent, and the new process is the child.

For example:

```text
bash
  |
  └── sleep
```

If `bash` has PID `1000` and starts `sleep`, then:

```text
sleep PID  = 12345
sleep PPID = 1000
```

I can check this using:

```bash
ps -o pid,ppid,cmd -p 12345
```

### Simple way to remember

> PID identifies the process itself. PPID identifies its parent.

---

# 4. Finding a Process with `pgrep`

`pgrep` allows me to find a process by its name.

For example:

```bash
pgrep sleep
```

If `sleep` is running, it returns its PID.

I can also use:

```bash
pgrep -a sleep
```

This gives me the PID along with the command.

---

# 5. Checking a Specific Process

Once I have a PID, I can inspect it using:

```bash
ps -fp <PID>
```

For example:

```bash
ps -fp 12345
```

I can also select specific columns:

```bash
ps -o pid,ppid,user,stat,%cpu,%mem,cmd -p 12345
```

This is useful when I want to quickly understand what a particular process is doing.

---

# 6. Creating a Background Process

I practiced creating a simple process using:

```bash
sleep 500 &
```

The `&` at the end tells the shell to run the command in the background.

The shell may show something similar to:

```text
[1] 12345
```

Here:

```text
[1]     = Job ID
12345   = Process ID (PID)
```

An important thing I learned here is:

> Job ID and PID are not the same thing.

---

# 7. Jobs

The `jobs` command shows jobs managed by my current shell.

I used:

```bash
jobs
```

Example:

```text
[1]+  Running    sleep 500 &
```

`jobs` is mainly concerned with jobs belonging to the current shell.

This is different from `ps`, which can show processes across the system.

---

# 8. Foreground and Background Processes

A process can run in the foreground or background.

### Background

```bash
sleep 500 &
```

The terminal remains available for me to run other commands.

### Foreground

```bash
sleep 500
```

The terminal waits for the command to finish.

I can move a suspended job to the background with:

```bash
bg
```

And bring a background job back to the foreground with:

```bash
fg
```

---

# 9. Suspending a Process with `Ctrl + Z`

If a command is currently running in the foreground, I can press:

```text
Ctrl + Z
```

This suspends the process.

I can then check:

```bash
jobs
```

It may show:

```text
[1]+  Stopped    sleep 500
```

To continue it in the background:

```bash
bg
```

To bring it back to the foreground:

```bash
fg
```

---

# 10. `top`

`top` provides a real-time view of running processes and system resource usage.

I can start it with:

```bash
top
```

It shows information about:

- CPU usage
- Memory usage
- Running processes
- Process IDs
- Process states
- Load average

To exit:

```text
q
```

---

# 11. Load Average

One of the things displayed by `top` is the load average.

Example:

```text
load average: 0.20, 0.15, 0.10
```

These represent approximately:

```text
0.20 → last 1 minute
0.15 → last 5 minutes
0.10 → last 15 minutes
```

Load average should not be confused with CPU percentage.

The number needs to be interpreted together with the number of CPU cores and the type of workload running on the system.

---

# 12. Process States

The `STAT` column in `ps` and `top` shows the state of a process.

Some common states are:

| State | Meaning |
|---|---|
| R | Running or runnable |
| S | Interruptible sleep |
| D | Uninterruptible sleep |
| T | Stopped |
| Z | Zombie |

A process being in `S` state is not automatically a problem. Many normal processes spend most of their time waiting for something.

---

# 13. Process Tree

Processes can have parent-child relationships.

I can view these relationships using:

```bash
pstree -p
```

For example:

```text
bash(1000)
  └── sleep(12345)
```

This shows that the `sleep` process was started by the `bash` process.

I can also check the parent directly:

```bash
ps -fp <PPID>
```

Understanding process trees is useful when troubleshooting services that spawn multiple child processes.

---

# 14. Stopping a Process with `kill`

The `kill` command is used to send a signal to a process.

For example:

```bash
kill 12345
```

By default, this sends `SIGTERM`.

It is important to understand that `kill` does not always mean "forcefully kill the process."

It actually means:

> Send a signal to the process.

---

# 15. SIGTERM

`SIGTERM` is a request for a process to terminate.

Example:

```bash
kill 12345
```

The application can handle this signal and perform cleanup before exiting.

For example, an application may:

```text
Receive SIGTERM
       ↓
Stop accepting new work
       ↓
Finish existing work
       ↓
Close files/connections
       ↓
Exit
```

This is generally preferable when gracefully stopping a service.

---

# 16. SIGKILL

`SIGKILL` is signal number 9.

It can be sent using:

```bash
kill -9 12345
```

Unlike `SIGTERM`, the process cannot catch or handle `SIGKILL`.

It is therefore a much more forceful way of terminating a process.

I should normally try graceful termination first:

```bash
kill <PID>
```

If the process refuses to terminate and there is a valid reason to force it:

```bash
kill -9 <PID>
```

---

# 17. Zombie Process

A zombie process is a process that has already finished execution, but its parent process has not yet collected its exit status.

A simplified view:

```text
Parent
  |
  └── Child
        |
        └── finishes execution
                |
                └── Zombie
```

A zombie is not actively running.

It remains as a process-table entry until the parent handles the child's exit status.

I can look for zombie processes using:

```bash
ps -eo pid,ppid,stat,cmd | grep Z
```

---

# 18. Orphan Process

An orphan process is a child process whose parent process has terminated while the child is still running.

For example:

```text
Parent
  |
  └── Child
```

If the parent exits:

```text
Parent ❌

Child
```

The child becomes an orphan.

Linux re-parents orphan processes to an appropriate system process so that they can still be managed and eventually have their exit status handled.

### Zombie vs Orphan

The easiest way I remember the difference:

```text
Zombie:
Child is finished
Parent is still there
Exit status has not been collected

Orphan:
Child is still running
Parent has already exited
Child gets re-parented
```

---

# 19. The `/proc` Filesystem

Linux provides process information through the `/proc` filesystem.

For example:

```bash
/proc/<PID>/
```

If the PID is `12345`:

```bash
/proc/12345/
```

contains information related to that process.

I can check the executable using:

```bash
readlink /proc/12345/exe
```

I can also look at file descriptors:

```bash
ls -l /proc/12345/fd
```

The `/proc` filesystem is very useful when investigating what a running process is actually doing.

---

# 20. Practical Troubleshooting Flow

If a Linux server is slow, I should not immediately restart the server.

A basic investigation can look like this:

```text
Server is slow
      |
      v
Check CPU / Memory
      |
      v
     top
      |
      v
Identify suspicious PID
      |
      v
   ps -fp PID
      |
      v
Check PPID and process state
      |
      v
Check logs / application
      |
      v
Decide what action is required
```

Useful commands:

```bash
top

ps aux

ps -fp <PID>

ps -o pid,ppid,stat,%cpu,%mem,cmd -p <PID>

pstree -p

pgrep <process-name>

jobs

kill <PID>

kill -9 <PID>
```

---

# 21. Commands I Practiced

These are the commands I practiced while learning Linux process management:

```bash
ps aux
ps aux | head

top

sleep 500 &
pgrep sleep
pgrep -a sleep

ps -fp <PID>
ps -o pid,ppid,user,stat,%cpu,%mem,cmd -p <PID>

jobs
bg
fg

pstree -p

kill <PID>
kill -9 <PID>

ps -eo pid,ppid,stat,cmd

ls -l /proc/<PID>
readlink /proc/<PID>/exe
ls -l /proc/<PID>/fd
```

---

# 22. Key Takeaways

After practicing these commands, the main concepts I want to remember are:

- Every running program is represented by a process.
- Every process has a PID.
- A process can have a parent, identified by its PPID.
- `ps` is useful for viewing process information.
- `top` is useful for real-time monitoring.
- `pgrep` helps find processes by name.
- `jobs` manages jobs associated with the current shell.
- `bg` continues a suspended job in the background.
- `fg` brings a job to the foreground.
- `kill` sends signals; it does not always mean force-killing.
- `SIGTERM` allows graceful termination.
- `SIGKILL` forcefully terminates a process.
- A zombie has finished but its exit status has not been collected by its parent.
- An orphan is still running after its parent has terminated.
- `/proc` provides detailed information about running processes.
- Process management is an important part of Linux administration and DevOps troubleshooting.
