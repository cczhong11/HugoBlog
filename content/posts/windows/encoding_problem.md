---
title: "Windows encoding error"
date: "2023-08-20"
description: "Stuck in the encoding problem in Windows python"
categories:
    - "python"
    - "gooey"
    - "windows"
    - "encoding"
---


# Context

I have a gradio app called [text-generation-webui](https://github.com/cczhong11/text-generation-webui) and user need to have python to start it. For normal people, they do not have python installed in their computer, so I need to package the python environment into the app. We use conda to manage the python environment and we use `conda-pack` to package the environment.

I create a GUI using gooey to start python cli in a subprocess. The code is like this:

```python
import signal

class YourClass:
    def __init__(self):
        self.stdout = []
        # 设置SIGTERM信号处理程序
        signal.signal(signal.SIGTERM, self.save_stdout)

    def save_stdout(self, signum, frame):
        # 保存self.stdout的逻辑
        with open('output.txt', 'w') as file:
            file.write('\n'.join(self.stdout))
        print("Saved stdout to file")

    def run(self, cmd):
        self.process = subprocess.Popen(
            cmd,
            stdout=subprocess.PIPE,
            stderr=subprocess.STDOUT,
            shell=True,
            text=True,
        )
        while True:
            output = self.process.stdout.readline()
            if output:
                self.stdout.append(output.strip())
                print(output.strip())

            if self.process.poll() is not None and not output:
                break

        return self.process.returncode

# 示例
your_class_instance = YourClass()
your_class_instance.run("your command here")

```

This code can capture all the output from the subprocess, it worked fine in the Mac but not working in the windows and it took me a while to figure out the reason.

# Problem

When I download the new model, it would throw the following exception:

```
C:\Users\tczhong\Documents\LLM\new-web-ui-one-click\text-generation-webui\download-model.py”, line 153, in get_single_file with tqdm.tqdm(total=total_size, unit=‘iB’, unit_scale=True, bar_format=‘{l_bar}{bar}| {n_fmt:6}/{total_fmt:6} {rate_fmt:6}’) as t: File “C:\Users\tczhong\Documents\LLM\new-web-ui-one-click\installer_files\env\lib\site-packages\tqdm\std.py”, line 1137, in exit self.close() File 
“C:\Users\tczhong\Documents\LLM\new-web-ui-one-click\installer_files\env\lib\site-packages\tqdm\std.py”, line 1299, in close self.display(pos=0) File 
“C:\Users\tczhong\Documents\LLM\new-web-ui-one-click\installer_files\env\lib\site-packages\tqdm\std.py”, line 1492, in display self.sp(self.str() if msg is None else msg) File 
“C:\Users\tczhong\Documents\LLM\new-web-ui-one-click\installer_files\env\lib\site-packages\tqdm\std.py”, line 347, in print_status fp_write(‘\r’ + s + (’ ’ * max(last_len[0] - len_s, 0))) File 
“C:\Users\tczhong\Documents\LLM\new-web-ui-one-click\installer_files\env\lib\site-packages\tqdm\std.py”, line 340, in fp_write fp.write(str(s)) File 
“C:\Users\tczhong\Documents\LLM\new-web-ui-one-click\installer_files\env\lib\site-packages\tqdm\utils.py”, line 127, in inner return func(*args, **kwargs) OSError: [Errno 22] Invalid argument
```

It always stuck in about 2% in the downloading and pretty strange.

# Debug

I spent a lot of time in the tqdm and I thought it is the problem since it is what exception said. However it is not the reason.

The subprocess itself crashed and the real exception is:

```
gbk' codec can't decode byte 0x8f in position 8: illegal multibyte sequence
```

It happened when reading stdout and it could not decode it.

# Solution

There are lots of places we can set up encoding.

## Popoen

We can set the encoding in the `Popen` function:

```python
self.process = subprocess.Popen(
    cmd,
    stdout=subprocess.PIPE,
    stderr=subprocess.STDOUT,
    shell=True,
    text=True,
    encoding='utf-8'
)
```

## Gooey

We can set the encoding in the gooey decorator:

```python
@Gooey(
    program_name="Text Generation",
    default_size=(800, 600),
    encoding='utf-8'
)
```

## python

When we start the python, we can force it using utf-8 encoding:

```bash
python -X utf8
```

## codecs writter

We can also use codecs to write the file:

```python
import sys
import codecs
if sys.stdout.encoding != 'UTF-8':
    sys.stdout = codecs.getwriter('utf-8')(sys.stdout.buffer, 'strict')
if sys.stderr.encoding != 'UTF-8':
    sys.stderr = codecs.getwriter('utf-8')(sys.stderr.buffer, 'strict')
```

# Conclusion

Using codecs writter solve the problem.


It is so frustrating to debug this problem and I hope this blog can help you if you have the same problem.

