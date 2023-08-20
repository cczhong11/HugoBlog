---
title: "Windows signal handler problem"
date: "2023-08-20"
description: "Stuck in the signal problem in Windows python"
categories:
    - "python"
    - "gooey"
    - "windows"
    - "signal"
---

# Context

I am using a gradio app inside Gooey to start python cli in a subprocess. I try to handle the signal in the subprocess, but it does not work in the Windows. It works fine in the Mac, but not in the Windows.

# Problem

I use the code like this:

```python


class ProcessHandler:
    def __init__(self) -> None:
        self.stdout = []
        self.process = None
        signal.signal(signal.SIGTERM, self.save_stdout)
        signal.signal(signal.SIGINT, self.save_stdout)

    def change_python(self, cmd):
        if "python" in cmd and sys.platform.startswith("win"):
            cmd = cmd.replace("python", os.path.join(f"{INSTALL_ENV}", "python.exe"))
        if "python" in cmd and sys.platform.startswith("darwin"):
            cmd = cmd.replace("python", os.path.join(f"{INSTALL_ENV}", "bin/python"))
        return cmd

    def save_stdout(self, signum, frame):
        with open("log.txt", "w") as f:
            f.write("\n".join(self.stdout))

    def run(self, cmd):
        self.stdout = []
        self.process = subprocess.Popen(
            cmd,
            stdout=subprocess.PIPE,
            stderr=subprocess.STDOUT,
            shell=True,
            text=True,
        )
        while True:
            output = self.process.stdout.readline()
            if self.process.poll() is not None:
                break
            if output:
                if output.strip() == "":
                    continue
                self.stdout.append(output.strip())
                print(output, flush=True, end="")

        return self.process.returncode

    def kill(self):
        if self.process:
            self.process.kill()

```

I use the `signal.signal` to handle the signal, but it does not work in the Windows. I tried to use `signal.SIGTERM` and `signal.SIGINT` to handle the signal, but it does not work.

# Solution

In windows, the signal handler for SIGTERM is [useless](https://stackoverflow.com/questions/47306805/signal-sigterm-not-received-by-subprocess-on-windows). Alternative solution is to send signal.CTRL_BREAK_EVENT to the process.

```python
@Goey(
    shutdown_signal=signal.CTRL_BREAK_EVENT,
)
def main():
    pass

class ProcessHandler:
    def __init__(self) -> None:
        self.stdout = []
        self.process = None
        signal.signal(signal.SIGBREAK, self.save_stdout)
        
```

Also in the pip, gooey is only 1.0.8, so to use shutdown_signal, we need to install the gooey from the github.



# Conclusion

Debug this problem spent me a lot of time, I hope this blog can help you to solve the problem faster.

# Learning

1. chatgpt is a good assistant to help you to debug the problem.
2. reading the source code is a good way to solve the problem.
3. set up mvp to debug is much faster than the real app.