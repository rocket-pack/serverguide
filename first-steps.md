First Steps
====

Great, so you've just got a server sorted out - you've probably got it as a VPS (Virtual Private Server), and have received an email or webpage with the access credentials you need - awesome! Let's get started:

First of all, let's check that you've got everything you need to work through the guide:

#### Ubuntu distribution on the server

All of the software we'll be installing on the server will work on any Linux distribution (popular server distros include Ubuntu, Debian, and RedHat derivatives), however _how_ this software is installed changes depending on which distribution is on your server. **This guide anticipates that you are using a recent release of Ubuntu server edition**. The reason this is suggested is that Ubuntu has a long-term support release, and ships with sensible packages and configuration preinstalled. 

* Debian: if you are using Debian, then all of the instructions in the guide **will work for you** - but Debian does not come as preconfigured as Ubuntu server, so you may need to do some research to find out what other configuration you might need to do on the server.
* RedHat & derivatives: If you are using Redhat, then the instructions in this guide **will not work for you** - this is because RedHat and similar distributions do not use the same method of installing software as Ubuntu does - for example, where Debian/Ubuntu uses `apt-get`, RedHat uses `yum`. You might still be able to fumble your way through the guide by working out which packages you need to install, and where the configuration files are located, but it's not something we can support.

#### An SSH client

Many static websites that operate on a shared host can get by with web or FTP access, but since we'll be setting this server up from scratch, we're going to need to use a standard remote access protocol called SSH. SSH allows us to log in to the server and run commands without needing physical access to it. If you're using OS X or most Linux distributions, you'll already be set up. If you're using Windows, you'll need to install an SSH client - [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/) is recommended.

#### Time

It's going to take a little while to work through this guide - less if you already know some of the steps, but if you're going to be reading through each bit of text while following the instructions, then you're probably going to need a good 6 hours or so in total to set up the server - it's also worthwhile adding some time for research and in case you get stuck. Don't get scared off though - you can stop at any step, so feel free to spread this over a week or so.


---

If you've checked all the boxes, then you're ready to get started! There's only a few things we need to do in this step - log in to the server, and then make sure that it's up-to-date before we start installing all our Rails stuff.

To log in to the server, you'll need a username and password, which should have been provided by your server provided. If you the username hasn't been provided, it's probably `root`.

You'll need to use the `ssh` command to log in to the server, which is in the following format:

``` bash
  ssh [user]@[host]
```

- you'll be prompted for the server password, and then be presented with a _command prompt_ on the server, ready to run commands - it should look something like this:

![Ubuntu Server: Just logged in](first-steps/logged-in.jpg)

> If you're using PuTTY, then you will log in to the server via a user interface window, rather than the `ssh` command - see the PuTTY website for details

Once you're at the command prompt on the server, just run two commands:

``` bash
  apt-get update
```

- this will contact the package serers, and update the 'repository' of packages, ensuring that we will be updating the latest versions of everything. 

To actually perform the update, run the following command:

``` bash
  apt-get upgrade
```

- this command will show you a list of packages that are going to be upgraded, and once you accept the prompt, there will be a small delay while new packages and downloaded and installed.

Once the upgrade is complete, you're good to go! You've logged in to the server, and made sure it is up-to-date. As a quick security tip, you might want to set up a reminder to run the upgrade step every couple of weeks - this makes sure your server is running the most recent software and security patches, to keep it bulletproof! 


** Next: [Installing Utilities](install-utilities)**

