Secure Access
===

Securing a server isn't really scary. For most servers, security isn't about making it bulletproof, it's about making it more effort than it's worth to hack into. To achieve this, we're going to make some basic configuration changes and add some packages that will help keep your server protected, without getting in your way.

You should still be logged in as the 'root' user from the 'first steps' section - if you're not, log in now, as we'll be running commands on the server throughout this section.

There's three main areas that we want to work on in this section, that we'll walk through one-by-one:

1. User management and access
2. SSH security
3. Ongoing Protection
4. Security updates

### User management and access

The user who you are logged in as at the moment is called the 'root' user. The root user is a highly-priviledged user who is able to perform any task on the server, including creating users, changing permissions, installing and configuring software, and adding, editing or removing any file. While this is useful for our current needs, obviously this isn't the sort of power we want anyone logging in to have. For day-to-day work on our server, we are going to create a less-privileged account, and then set up a handy tool called 'sudo', that will let us 'elevate' our low-privilege account to perform root-user actions, but only when required, and when the correct password is provided - still flexible, but much more secure than just using the root account all the time.

First, let's create our user. What you call this user is up to you - some people like using 'deployer', or 'admin' as a username. I prefer to use the name of our application, just in case I need more users further down the track. So, if you are using the example application, we are going to use 'dogbook' as the username.

``` bash
adduser deployer
```

![Ubuntu Server: Adding a non-privileged user](secure-access/adduser.jpg)

The `adduser` program will ask you for a few details, the most important of which is the password. This password is very important, so you shouldn't skimp on it's complexity - aim for something at least 16 characters long, with a mixture of alphanumeric characters and symbols. Using a phrase that is easy for you to remember, but hard for others to guess, can also be a good idea. You'll also be asked for some details about the 'user', such as their full name, email and location - you can leave all of these blank, they're not necessary for our needs.

Next, we'll set up the `sudo` tool, so that we can run commands with root-level access when necessary - often when installing system software where we need to access files that our user account is not normally allowed to access. When this happens, we can just prefix the command we want to run with 'sudo', which will allow us to perform the action. On Ubuntu, `sudo` comes preinstalled - we just need to set it up so that our newly-created user is allowed to use it, and to do that, we've going to use a tool that `sudo` provides us with to maintain who can do what - `visudo` - here's what it looks like when you run the `visudo` command:

![Ubuntu Server: visudo](secure-access/visudo.jpg)

Because we want full control over how `sudo` is run, we're going to clear out all the rules in the file at the moment - all the ones with a hash ('#') in front of them are comments anyway. The 'vi' bit of 'visudo' means that it launches a [Vim](http://en.wikipedia.org/wiki/Vim_(text_editor))-like editor, which is a powerful and efficient text editor - if you don't know your Vim though, don't worry - just type `:%d` to delete all lines in the file. Finally, we're going to add our own rules - one for the 'root' user, and one for the user we just created. Here are the rules, which you can enter by hitting the `i` key on your keyboard, and then pasting in.

``` bash
root    ALL=(ALL) ALL
dogbook  ALL=(ALL) ALL
```

Once you've pasted these, and updated the final rule with the correct username if you didn't create the 'dogbook' user, you can save and exit, by hitting `Esc`, and then typing `:x`.

### SSH security

You've got a secure user set up now, so the next step is to lock down SSH. Remember, SSH is the most common method of logging into a server to manage it, so we want to do our best to make sure that **we're** the only ones who can actually log in.

The first thing we're going to do to secure SSH access is to disallow the root user from logging in via SSH. The reason for this is twofold. First, it will prevent a lot of widespread attacks that try and log in to unsecured servers using the root user and a range of typical passwords. Secondly, now that we've set up `sudo` for your non-privileged user, there's no need to actually log in as root anymore - so we might as well disable it.

To make this change, we need to update the configuration file for the SSH server, which is located at `/etc/ssh/sshd_config`, and add the line:

``` bash
PermitRootLogin no
```

The next security measure we're going to do involves _public keys_. In case you haven't come across them before, public keys are a way of accessing a server without requiring a password - in a real life analogy, using a password to access a server is like knocking on a door - why knock, when you can simply use your key and walk right in?

To allow you to log in with your public key,you first need to find it on your computer. If you are using OS X or Linux, you should already have a public key created by your system in a hidden folder within your home folder, in a file called `id_rsa.pub` - open up a terminal on your computer and run `cat ~/.ssh/id_rsa.pub` to output the contents of the fille, and copy this output - this is your public key. If it looks like your public key file does not exist, simply run `ssh-keygen` and follow the prompts to generate your public key.

> If you are using Windows,you're most likely using PuTTY to
> login to your server. Because Windows doesn't support public
> keys natively, you will need to generate and set up a public 
> key within PuTTY itself. See [this blog post]
> (http://katsande.com/using-puttygen-to-generate-ssh-private-> public-keys) for more details on how to do that.

So,right now you should have your public key copied - we now need to add it to a file on the server called `authorized_keys` - this file is just a list of public keys that are allowed to log in - each public key on a line. To add your public key, start by logging in to your server as your application user, using the password you set up earlier. Once you are logged in, we need to edit the authorized keys file:

``` bash
vim ~/.ssh/authorized_keys
```

Right now, this file should be empty, so we just want to paste our key in. Hit the `o` key - in vim-speak, this means "drop onto the next line and switch to insert mode". You can then paste in your public key, and then hit `Esc` then `:x` to save and exit. Once your public key has been added to the `authorized_keys` file, try logging in to the server as the application user - this time, you shouldn't be prompted for your password - you should be let right in!

As a  final step, we need to restrict our SSH access just an little more, so that our application user is the only user who may log in via SSH. To do this, open up your SSH config file again (`vim /etc/ssh/sshd_config`), and then hit `G` to jump to the bottom of the file and then hit `o` to start a new line and enter insert mode, then add the following line, replacing "[application username]" with your application username - e.g. 'dogbook':

``` bash
AllowUsers [application username]
```

And then save the file by hitting `Esc` then `:x`. Once this file is saved, we want to restart the SSH server so that our config file changes come into effect - incidentally, this will be the first time we'll be using `sudo` to elevate our application user's privileges - now that we've prevented the root user from logging in, we'll be using `sudo` a lot more. To restart SSH, run the following command - after it finishes, make sure you remain logged in, just in case there's any problems with the config.

``` bash
sudo service ssh restart
```

Once the service has restarted, open up a new terminal window on your computer that we can use to test the changes we've made. **Don't forget to stay logged in to the server in at least one window!**.

First, let's make sure that we can't log in as root - try running this, and make sure that it doesn't allow you to log in to the server:

``` bash
ssh root@your-server-ip
```

Hopefully that's working for you (well, not working, but working for our purposes) - if not, just re-review the bit above where we set `AllowRootLogin` to `No`.

Next, let's make sure that we can't log in as a user other than your application user - a good test user to use is the `nobody` user, which is a placeholder user created by Ubuntu when it is installed. Try logging in as the `nobody` user - because of the `AllowUser` restriction we put into place, you should not even get to a password prompt:

``` bash
ssh nobody@your-server-ip
```

If both of these tests are working out, then all the SSH security restrictions we've put into place are working - well done! The server is now accessible to anyone trying to log in as your application user with either a public key that has been added to the server, or the correct password, but nobody can log in as any other user. Nice!


### Ongoing Protection

We've got our server generally protected from the bad guys accessing the server, but now it's important that our server is set up to keep any loopholes closed over time. We're going to do that in a couple of ways, but the most important method is a _firewall_ - a piece of software (there are also hardware firewalls, but this is generally something you deal with at the datacenter level), whose role is to only allow certain requests through to the server (for example, allowing SSH, HTTP and HTTPS traffic). We'll deal with that in a moment, but first I'm going to go over some concepts about what we're protecting our server _from_.

Remember, the goal here is to protect our server from casual, drive-by attacks using automated tools or hackers with a low-level of expertise, and we've established that the best way to prevent your server from being hacked is to turn it into a difficult target to get into. One of the first steps of any hacker, and the role of most scripts/hacking tools, is _information gathering_ - running various tests to try and find out versions of software installed on our servers, how the server is set up, and where the weak points are. Once the weak points are known, it is actually very easy to do some damage - whether that is via a denial-of-service attack, where the server is just made to do a lot of work until it is unable to respond to traffic, or a full-blown penetration. Therefore, one of the best things we can do to protect our server in the long-term is to dynamically prevent this information from being gathered.

The first step we're going to take is to set up a firewall. A firewall's main job is block malicious traffic, based on established rules, which may allow or disallow traffic in a number of ways (for example, by IP address or port number).

> #### Don't know what a port is?
> A 'port' on a server is a bit like a doorway - except that a server has roughly 10,000 'doorways' available. Each port may have software listening 'on' it, and traffic may go into and out of a port. When a firewall is protecting ports, it is simply closing the door, and not allowing any traffic in or out. Ports are not neccessarily 'locked' to a piece of software, and you can generally change the port that any one application will listen on - for example, the SSH server generally listens on port `22`, however you may change the port to `1022`, or any other port you like. Similarly, the web server we'll be setting up soon listens on two ports - `80` and `443` by default, that handle HTTP (unencrypted) and HTTPS (encrypted) web traffic.

Ubuntu Server comes built in with a really nice firewall tool called `ufw` (which stands for **U**buntu **F**ire**w**all). This tool acts as a layer between us and the de-facto software firewall on Linux distributions, which is called `iptables`. `iptables` can be used on it's own, however keeping rules up to date can be quite complex and difficult to manage. `ufw` will handle keeping track of our firewall rules, and enabling/disabling our firewall as required. `ufw` should already be installed - if you want to be sure, just run `which ufw` - if this returns a path to a file, then `ufw` is installed.

> If `ufw` is not installed, you can install it using `sudo` and `apt-get` (which we used in the first section of this guide): `sudo apt-get install ufw`.

Before we start blindly adding rules, it's a good idea to think about the types of traffic we need to allow, and whether this traffic needs to be allowed in and or, or one or the other - this is important, because if we're not careful, we could firewall ourselves out of our own server!

> I'll say it now - if you do firewall yourself out of your own server, don't panic! Most VPS or server providers are able to provide you with some kind of secondary, root-level access. For example, [Linode](https://linode.com) provides the [Lish console](http://library.linode.com/troubleshooting/using-lish-the-linode-shell). If your host doesn't provide you with this access, they probably have access themselves - just get in touch with them and explain the situation - it happens more often than you might think!

So, we have three ports that are used by our server that we need to make sure are allowed through our firewall:

1. Port 22 - SSH (in and out traffic)
2. Port 80 - HTTP (in and out traffic)
3. Port 443 - HTTPS (in and out traffic)

Each of these ports needs to have inbound AND outbound traffic to ensure everything runs properly. Let's go ahead and add the `ufw` rules for that now:

``` bash
$ sudo ufw allow ssh
$ sudo ufw allow http
$ sudo ufw allow https
```

Simple as that! The last thing we need to do is 'enable' these rules, so that they come into effect. Take a deep breath, and run the following command to activate your firewall:

``` bash
sudo ufw enable
```

If your terminal is still responding, great! Looks like you still have un-firewalled access! It's worth keeping in mind how to manage your firewall - as you install more services on your server, you might need to add more firewall rules. You can do this by running `sudo ufw allow [service name or port number]` in a terminal, followed by `sudo ufw enable` to activate the rules. If, for whatever reason you need to temporarily disable your firewall, you can run `sudo ufw disable` - just don't forget to re-enable the rules!


- penetration testing
- fail2ban
- ports & how they are hacked
- what is a firewall & how it works
- installing
- configuring
- ongoing firewall config examples

### Security updates

- why are updates important
- install unattended updates
- configure
- about ubuntu repositories + which to enable

If you've worked your way through these steps, your server should be pretty secure - it has a fixed list of users who may log in, with each of these users having a nice hard-to-brute-force (guess) password, and you've got a layer of protection to prevent any attackers from being able to force their way into the system, along with security updates to make sure any loopholes will be closed quickly. Of course, these protections may not be adequate if somebody chooses to launch any sort of complex, widely-distributed or prolonged attack on your server, however you have built in enough security to prevent all but the most determined attackers from gaining access.

**Next: [[Installing Utilities|install-utilities]]**

