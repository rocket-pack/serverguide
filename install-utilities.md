The server we're setting up is designed to be secure-enough, and set up to require as little maintenance as possible. Despite this, it's inevitable that you will need to log in to the server every now and then, and when you do, having certain utilities installed will make your life a lot easier. This section covers some common Linux utilities such as `cat`, `grep`, `tail`, `du`, and `df`, finishing up with `htop`, which is a great way to quickly get a snapshot of your server's performance.

### Finding things in files

I guaruntee that if you do any sort of development, a regular task you'll need to perform is finding bits of text in very long, or multiple files. The first set of tools we're going to talk about will help with this. If you already know about these tools, feel free to skip over this.

#### `cat`

The `cat` command is handy for quickly inspecting files. It generally takes one argument - the path to the file - and outputs the contents of that file to the terminal window. It can be combined with `grep` (which we're going to cover in a moment) to search for particular lines in a file, or `less`, to provide the ability to 'scroll' through a long file.

Here are some examples:

Output the contents of your Bash configuration file:
``` bash
cat ~/.bashrc 
```

Look for your username in the list of system users:
``` bash
cat /etc/passwd | grep "[your application username e.g. dogbook]"
```

Scroll through a long file using `less`:
``` bash
cat /var/log/syslog | less
``` 
<small>You can type `q` to exit less.</small>

#### `grep` 

The `grep` command is great for looking for things. It's one of those tools that, if you take the time to properly master, can save you all sorts of time. For our purposes, we're just going to cover searching for simple strings of text - however with full regexp support, and a swathe of other options, `grep` is a very useful tool to learn.

Normally, `grep` is used with a pipe character (`|`) - in most *sh shells. This means that the output of the command preceeding the pipe character will be redirected to the command following. In the second example for `cat`, we 'redirected' the contents of the `/etc/passwd` file to the `grep` command to search for our application username. This is the core of what `grep` does, however there's still a couple of common techniques that you can use:

Search for a string in a file (without using the pipe character):
``` bash
grep /etc/passwd "[your application username, e.g. dogbook]"
```

Search for a string in a folder recursively:
``` bash
grep --recursive /etc/ "[your application username, e.g. dogbook]"
```

#### `tail`

`cat` is all well and good, but if you're dealing with a massive file that you need to search through, or you want to keep an eye on the file as it is being written to (for example, an application log), then `tail` is perfect. Rather than reading the entire file in one go, `tail` will read the file as it changes, outputting the contents to your terminal window.

Keep an eye on the `syslog` (the 'syslog' is the system messages log file on Ubuntu):
``` bash
tail -f /var/log/syslog
```

Keep an eye on the `syslog`, and use `grep` to filter for lines with the number "2" in them:
``` bash
tail -f /var/log/syslog | grep "2"
```

### Checking on your disks

Aside from checking log files every now and again, another common task will be checking the disk space on your server, and cleaning up any large files. Log files in particular, if ignored over time, can easily fill up a disk, and it can be tough to recover from a 100% full disk drive. There's really only two tools you need for keeping track of disk space on Linux, and they're both really easy to use:

#### `df`

`df` is a tool that will list the mounted volumes on a disk, their capacity, and the space free on each. It's a good idea to check in every now and then, and if you notice a disk starting to get full, it's time to move onto `du` to track down what's taking up all your disk space.

The use of `df` is really simple. You can use it without any arguments if you like, however, passing '-h' as an argument will tidy up the numbers to make the output much easier to read:

``` bash
df -h
```

#### `du`

When you need to work out what's taking up space on your disk, `du` becomes really handy. While there are more efficient options to simply find large files on disk (try running `man find` for more information on this), `du` is great for when you already suspect certain folders for large files, and want to figure out where all the large files are. The common arguments we pass to `du` are '-s', and '-h' - these make the output of `du` easier to read, by providing a summary of the size, and outputting the size of files in human terms - megabytes, gigabytes and so on.

To list all the files and folders in the log file:
``` bash
du -sh /var/log
```

To find the total size of the current directory:
``` bash
dh -sh .
```

To list the size of files in the current directory:
``` bash
du -sh *
```

### Monitoring resources

The last utility you're likely to find yourself using a lot of `htop`. `htop` builds upon `top`, which is a tool for listing running processes, and how much processing time and memory each is consuming. To see the output of `top`, simply run `top` with no arguments. `htop` provides similar functionality to `top`, but provides some handy additions, such as color highlighting, more helpful statistics, and the ability to easily control each process - for example, sorting and searching for processes, and killing out-of-control processes. 

First of all, to install `htop`, just use `apt-get`:

``` bash
sudo apt-get install htop
```

And then run the program by running `htop`, where you'll see output like this:

![htop](utilities/htop.png)

Once `htop` is open, you can use a number of keyboard commands to adust what you see:

* Sorting:
* Searching:
* Killing processes:

If you run into any problems with your server, it'll be `htop` that will be a good first port of call - take a look, and check for any processes that have been running for a long time, or are using a lot of CPU time or memory.

While it may be the packages that we install soon that will form the backbone of your server, it'll be these utilities that you use most often. They're a great place to start when debugging any performance problems, and offer a great deal more power should you wish to learn more about them. If you forget how to run any of these utilities, or if you want to find out how you can perform more advanced tasks with them, just use `man` on your server (`man` stands for 'manual'), followed by the command you want to know more about - for example, `man du` will tell you more about the `du` command.

**Next:** [[Installing Ruby|ruby]]




