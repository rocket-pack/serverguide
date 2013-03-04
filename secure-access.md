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

### Ongoing Protection

### Security updates

If you've worked your way through these steps, your server should be pretty secure - it has a fixed list of users who may log in, with each of these users having a nice hard-to-brute-force (guess) password, and you've got a layer of protection to prevent any attackers from being able to force their way into the system, along with security updates to make sure any loopholes will be closed quickly. Of course, these protections may not be adequate if somebody chooses to launch any sort of complex, widely-distributed or prolonged attack on your server, however you have built in enough security to prevent all but the most determined attackers from gaining access.

**Next: [[Installing Utilities|install-utilities]]**


