---
title: Pipes in Linux
date: 2016-08-13 01:38:22
tags:
  - Linux
  - Pipes
  - Python
---

In this blog, I'd like to share about [Pipes](https://en.wikipedia.org/wiki/Pipeline_(Unix) - an interesting feature of Unix/Linux operating systems (it's available in other systems too). Having talked about Pipes in brief, I will do the implementation in Python. So let's get started!

### Pipe

###### Brief History

Pipes are the eldest IPC tools and were introduced by [Douglas McIlroy](https://en.wikipedia.org/wiki/Douglas_McIlroy) after he noticed that most of the time they were processing the output of one process as the input to another. Later, [Ken Thompson](https://en.wikipedia.org/wiki/Ken_Thompson) added the concept of pipes to the UNIX operating system.

###### About

In simple terms, a *pipe* is a method of connecting the standard output of one process to the standard input of another. A quick example:
```sh
ls . | sort
```
In the above shell command (reading from left to right), we are passing the output from `ls` command as an input to `sort` command. The combined output is a current directory's content in a sorted order. The vertical bar ( `|` ) is a pipe character. It acts as a method of one-way communications (or half-duplex) between processes.


They are of basically two types : **Anonymous Pipes** and **Named Pipes** (FIFO).

The one we just saw, was an *anonymous pipe* or *half-duplex pipe*.

###### Pipe creation
When a process creates a pipe, the kernel sets up two file descriptors (read and write) for use by the pipe. A pipe initially connects a process to itself and any data traveling through the pipe moves through the kernel. Under Linux, pipes are actually represented internally with a valid [inode](https://en.wikipedia.org/wiki/Inode) which resides within the kernel itself, and not within the bounds of any physical file system. Well, you might question, what's the point of pipe if they connect a process to itself. Are they just going to communicate with itself? Well, the answer is no. Pipes are useful in case when we fork a child process and as we know a child process inherits any open file descriptors from parent, allowing us to setup a multiprocess communication (in this case between child and parent process). As both the processes have access to file descriptors, a pipeline is setup.

One important thing we should note that, since the pipe resides within the confines of the kernel, any process that is not in the ancestry for the creator of the pipe has no way of addressing it. This is not the case with named pipes (FIFOS), which we will discuss next.

###### Named Pipes
Unlike anonymous pipes, a named pipe exists in the file system. After input-output has been performed by the sharing processes, the pipe still exists in the file system independently of the process, and can be used for communication between some other processes.
We can create a named pipes either using `mkfifo` or `mknod` shell commands (Python has an inbuilt method which we will see during implementations).
Just like a regular file, we can set file permissions on a named pipe. Checkout mode from `man mkfifo`.
A quick example of named pipes that we might have often come across.
```sh
cat <(ls -li)
```
Here, the output from `ls -li` is redirected to a temporary named pipe, which shell creates, names and later deletes. Another fun example is to create a very basic shared terminal. Let's try it out.
The idea is to create a named pipe and then use two separate `cat` process to read/write data from/to the named pipe.

* Creating a named pipe (from console) :
```sh
mkfifo named_pipe_file_name # create a named pipe
# or
mknod named_pipe_file_name p
```

  If you observer closely the output from `ls -l` looks like:
```sh
prw-r--r--. 1 tushar tushar 0 Aug 13 05:32 named_pipe_file_name|
```
You may have noticed an additional `|` character is shown next to `named_pipe_file_name` and the file permission starts with `p`. This is a Linux clue that the `named_pipe_file_name` is a pipe.

* Using it:
```sh
cat <named_pipe_file_name # read
# Run the next command in a separate terminal instance.
cat >named_pipe_file_name # write
# Now type your heart out! :)
```

  You might notice that after the first command the execution appears to be blocked. This happens because the other end of the pipe is not yet connected, and so the kernel suspends the first process until the second process opens the pipe.

I hope that was a very simple usage of named pipes and helped you understand named pipes.

Now (for fun), let's implement pipes in Python. Since the code is in Python, I need not explain every line from the code. It should be readable. :)
The basic idea is to create two processes (parent and child) and let parent read the data written by child process.
###### Implementation in Python2.7

* Anonymous pipe

  ```python
  import os
  import time

  def child(pipeout):
    while True:
        time.sleep(1)
        os.write(pipeout, "Hello from child!")

  def parent():
      # The two file descriptors.
      pipein, pipeout = os.pipe()
      """
        Note: The pipe() call must be made before a call
        to fork or the descriptors will not be inherited
        by the child.
       """
      if os.fork() == 0:
          child(pipeout)
      else:
          while True:
              msg = os.read(pipein, 128)
              print('Parent {} got {} at {}'.format(os.getpid(),
                                                    msg,
                                                    time.time()))

  parent()
  ```

* Named pipe

 The implementation is almost similar except the fact that instead of descriptors, we have access to named pipe file name and we use it to perform I/O operations.

  ```python
  import os
  import time

  pipe_name = 'named_pipe_file_name'

  def child():
      # Note the same file name is being used for I/O.
      pipeout = os.open(pipe_name, os.O_WRONLY)
      while True:
          time.sleep(1)
          os.write(pipeout, 'Hello from child!\n')

  def parent():
      pipein = open(pipe_name, 'r')
      while True:
          msg = pipein.readline()[:-1]
          print('Parent {} got {} at {}'.format(os.getpid(),
                                                msg,
                                                time.time()))

  if not os.path.exists(pipe_name):
      os.mkfifo(pipe_name) # Creates a file on disk.

  pid = os.fork()
  if pid != 0:
      parent()
  else:
      child()
  ```

Phew! That was fun!

Cheers!
