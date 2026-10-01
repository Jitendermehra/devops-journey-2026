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
