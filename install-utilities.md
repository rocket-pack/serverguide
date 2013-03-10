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

TODO

### Checking on your disks

TODO

#### `df`

TODO

#### `du`

TODO

### Monitoring resources

#### `htop`

TODO




