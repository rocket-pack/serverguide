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
adduser dogbook
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

Right now, this file should be empty (unless your hosting provider has already copied their public key to your server), so we just want to paste our key in. Hit the `o` key - in vim-speak, this means "drop onto the next line and switch to insert mode". You can then paste in your public key, and then hit `Esc` then `:x` to save and exit. Once your public key has been added to the `authorized_keys` file, try logging in to the server as the application user - this time, you shouldn't be prompted for your password - you should be let right in!

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

If your terminal is still responding, great! Looks like you still have un-firewalled access! However you will not be able to run 

``` bash
sudo apt-get install [package-name]
```

or anything else that requires outbound access on any port. This is because the newly added ports to the firewall rules dont come into effect until the server is restarted. With that in mind, please restart your sever before continuing the guide:

``` bash
sudo reboot now
```

It's worth keeping in mind how to manage your firewall - as you install more services on your server, you might need to add more firewall rules. You can do this by running `sudo ufw allow [service name or port number]` in a terminal, followed by `sudo ufw enable` to activate the rules. If, for whatever reason you need to temporarily disable your firewall, you can run `sudo ufw disable` - just don't forget to re-enable the rules!

The other tool we want to install alongside our firewall is called `fail2ban`. While the firewall protects us from information gathering and hacking on ports that we don't need to be exposed to the outside world, `fail2ban` protects us from brute-force attacks on the services we DO need to be accessible to the public.

> A brute force attack is more or less what it says - a person or automated script trying a combination or usernames and passwords, as well as a number of other common hacks, until access is gained.

`fail2ban` actually works in quite a clever way. It will monitor the services we run on the server, the most important of which are SSH (which we already have set up), and the web server (which we'll be installing soon). It is programmed to recognize harmful activities, such as a large amount of incorrect login attempts, and can take actions to prevent these activities, such as banning the IP address that these requests are originating from for a certain period of time.

It's already set up to protect all the services we'll be running on our server, all we need to do is install it on our server. We've already used `apt-get` for upgrading our server, and now we're going to use it for actually installing a new 'package', A package is usually a piece of software, that may also pull in packages it depends on. `apt-get` handles downloading, installing, upgrading and removing packages for us - all we need to do is provide the name of the package we want to work with. In this case, we'll be installing the 'fail2ban' package:

``` bash
sudo apt-get install fail2ban
```

Once this is installed, you're all done with your ongoing protection - `fail2ban` and `ufw` should work together nicely to prevent brute-force and information-gathering attacks from making your server vulnerable to a full-blown exploitation.

### Security updates

As we begin installing many more packages and changing configuration, we expose more and more parts of our server to attack. This is inherently part of adding software, as each new tool we add increases the possibility that a vulnerability exists in that package which could be the entry point for an intruder. Generally, though, the Ubuntu, Debian and Linux communities are all very aware of security issues and closing any loop holes which are discovered - often faster than their equivalents in the OS X and Windows worlds.  Because vulnerabilities are being fixed all the time, keeping our server up-to-date is one of the best ways we can protect our server. If you have a few servers, though, remembering to log in once a week (at least!) to update and upgrade the packages you have installed can be really tough!

To make it easier for our server to remain protected, we're going to install one last tool, which will automatically upgrade our packages on a scheduled basis, to make sure that we're always up-to-date with security patches, without you needing to take any action yourself.

Just like `fail2ban`, we're going to start by installing the package to perform this task - it's called `unattended-upgrades`:

``` bash
sudo apt-get install unattended-upgrades
```

Unlike `fail2ban`, however, this package isn't going to work for us straight away - we need to configure a couple of files to make sure that our server upgrades it's packages automatically:

First, the file that adjusts settings for perfoming upgrades - open `/etc/apt/apt.conf.d/10periodic` with VIM:

``` bash
vim /etc/apt/apt.conf.d/10periodic
```

And update the file to have the following settings. In VIM, you can delete a line by pressing `dd` (i.e. the `d` key, twice), and then the `o` key to drop a line and go into insert mode. Or, you can just hit `i` to go into insert mode and use the backspace key as you normally would:

``` 
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
```

Once the settings are updated, you can save the file and exit VIM by hitting `Esc` and then `:x`.

Finally, you need to adjust another file to adjust which updates you wish to install. `apt` has a concept of `repositories`, which are different places that packages may be sourced from - for example, the repository we're insterested in is called `security`, and only contains important security updates for packages. Other repositories may include different packages altogether, for example Ubuntu's `non-free` repository, which contains packages that are not available for free and open release - usually audio and video codecs. All we need to do, though, is change the file which controls which repositories we want to install automatic updates from so that we only automatically install security updates, and not every update that comes along:

``` bash
vim /etc/apt/apt.conf.d/50unattended-upgrades
```

And remove the line that says `Ubuntu lucid-updates` from the `Unattended-Upgrade::AllowedOrigins` so that it looks like this:


``` bash
Unattended-Upgrade::Allowed-Origins {
        "Ubuntu lucid-security";
};
```

And then save the file and exit VIM by hitting the `Esc` key and then typing `:x`. There's no need to restart any services this time around, because these configuration changes will automatically come into effect next time the unattended upgrades are run.

> #### Dangers of automatically upgrading packages
> Some Ubuntu users disagree with automatic package upgrades, as it is possible that new versions of packages could introduce new, unwanted features, or break your configuration. Whether you choose to set up automatic upgrades or not is your choice - the main thing is to make sure your server is always up-to-date. We've chosen a pretty safe approach here, by only enabling automatic upgrades from the `security` repository. These packages should only have important security patches, and should not introduce any new behaviour or configuration rules. Still, it's best you know there is a choice here!

---

If you've worked your way through these steps, your server should be pretty secure - it has a fixed list of users who may log in, with each of these users having a nice hard-to-brute-force (guess) password, and you've got a layer of protection to prevent any attackers from being able to force their way into the system, along with security updates to make sure any loopholes will be closed quickly. Of course, these protections may not be adequate if somebody chooses to launch any sort of complex, widely-distributed or prolonged attack on your server, however you have built in enough security to prevent all but the most determined attackers from gaining access.

**Next: [[Installing Utilities|install-utilities]]**


### Attribution:

It's been tough to find some clear guides that cover enough server security to make sure that the server is locked-down enough, without going overboard for the situation (assuming your not deploying a banking application!). This portion of the guide is heavily based on [this blog post](http://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers), and has been extremely helpful in the writing of thise guide. Thanks, [Bryan Kennedy](http://plusbryan.com/)!

