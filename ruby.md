### Installing Ruby

Installing Ruby is one of the most contentious and opinionated areas of running a Rails server - for good reason. Over time, various different methods have emerged, each with it's own advantages and disadvantages. While evaluating the best method to use, the following points were considered:

1. Upgradeability
2. Stability
3. Simplicity

Given the following considerations, here is how the three main Ruby deployment techniques measure up:

#### Ruby from source

Installing Ruby from source has always been the 'recommended' way of installing Ruby - but it has it's downsides. While installing from source may seem attractive, tools such as RVM and rbenv exist for the very reason that setting up Ruby (in the _right_ way) is not the simplest process to get right the first time. Still, for many, the simplicity of _just_ having Ruby wins out.

* Upgradability: 2/10 - when Ruby is installed from source, it's a manual copy of files invoked by the build process. There's no built-in support for upgrading, so you're going to have to re-download the latest version, and install it by hand - and if you don't use the same settings, you may well find you've got two versions of Ruby fighting for supremecy!
* Stability: 10/10 - obviously when Ruby is installed straight from source, there's very little to go wrong. If you can cope with the manual upgrade process and know a little bit about configuring and compiling tools like Ruby, then this solution can be bulletproof.
* Simplicity: 5/10 - installing Ruby from source means there's no middleman to wrap your head around. That's an upside _and_ a downside though, as often having that middleman makes using Ruby a lot easier.

#### RVM

RVM is definately the longest-running Ruby-switching project, and continues to be excellently supported. It supports multiple Rubies, gemsets, and has documentation for the areas where you're likely to get stuck. It is quite complex to understand though, and is a little-overfeatured for a production server.

* Upgradability: 10/10 - RVM itself can be updated, as can Rubies
* Stability: 7/10 - RVM is a stable and long-running project, but it's got some quirks that seem to present themselves at the most inconvenient times. Confusion over system-wide vs. per-user installs can also affect your experience with RVM.
* Simplicity: 3/10 - while it's easy enough to see that RVM works, it's a little less tricky to see what's actually going on behind the scenes. Some of RVM's features, such as support for bundling it's own copies of some dependencies, can hide the root cause of problems, and it's quite complicated to set up in a multi-user install.

#### rbenv


rbenv was created by developers who grew a little disenfranchised with the way RVM used to do things (RVM has improved a lot and changed how it does things since those days). They started from the ground up with a shell-script based tool that was designed to do one thing, and do it well - switching Rubies. Functionality is added by installing plugins, meaning that you don't get the feature overload that you do with RVM - but expect to spend a little more time on StackOverflow than you might with RVM.

* Upgradability: 8/10 - rbenv installs itself as a git repository, so updating is as simple as a `git pull`. If you install the `ruby-build` plugin, then you can also easily install, upgrade and remove different versions of Ruby - it's not quite as nice as RVM's system though.
* Stability: 8/10 - rbenv is just shell scripts - effectively, there's less to go wrong. It's not as mature as RVM, though, so don't rule out bugs.
* Simplicity: 7/10 - while not having the same extend of 'just working' as RVM, it's not far off, and does so with quite a simple framework that isn't more complex than it should be. 

Unfortunately, there's no silver bullet when it comes to installing Ruby on a production server - it's up to you to balance up the benefits and drawbacks of each method, and choose the one that matches your needs the most - for example, if you have a great deal of experience using RVM, perhaps it would be best to continue using that on your server!

This portion of the guide will cover how to install each of these tools, but the assumption from here on in is that **we are using rbenv** - once you're installed and you've got a Ruby set up though, there shouldn't be any differences.

> **Why did you choose rbenv?** 
> We've tried RVM, rbenv and Ruby from source on production servers, and all have had their problems. rbenv, though, is so easy to understand and simple, that we feel it's the best tool for us to use - it gives us the flexibility of being able to trial different versions of Ruby and upgrade easily, without overburdening us with features.

#### Installing Ruby from source

* Head over to the [Ruby-lang downloads page](http://www.ruby-lang.org/en/downloads/), and find the version of Ruby you'd like to install - right click and copy the link to the file, then head back over to your SSH session, and use `wget` to download the file (e.g. `wget ftp://ftp.ruby-lang.org/pub/ruby/2.0/ruby-2.0.0-p0.tar.gz`).
* Extract the file you downloaded: `tar -xzf ruby-2.0.0-p0.tar.gz`
* Install the libraries that Ruby needs to run:
  ``` bash
  sudo apt-get --no-install-recommends install build-essential openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev libgdbm-dev ncurses-dev automake libtool bison pkg-config libffi-dev
  ```
* Configure Ruby: `cd ruby-2.0.0-p0` and `./configure`
* Compile: `make`
* Install: `make install`
* `ruby -v` should now return the version of Ruby you've got installed.

#### Installing RVM for the 'dogbook' user

First, head over to the [RVM website](https://rvm.io), and have a read of the features that RVM supports, and get an idea of how things fit together. When you're ready to install:

* Install cURL, which RVM needs to install: `sudo apt-get install curl`
* Run the RVM install script: `\curl -L https://get.rvm.io | bash -s stable --ruby=2.0.0 --autolibs=enable`. This script may prompt you to install extra dependencies while it runs.
* Follow the instructions at the end of the installer if necessary to install RVM into your shell session. 
* Log out of your SSH session and then log back in to ensure everything is reloaded - then run `ruby -v` to check that Ruby has been installed.

#### Installing rbenv 

* First of all, install the same dependencies that Ruby from source requires: `sudo apt-get --no-install-recommends install build-essential openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev libgdbm-dev ncurses-dev automake libtool bison pkg-config libffi-dev`
* Next, 'install' rbenv by cloning a repository from Github: `git clone git://github.com/sstephenson/rbenv.git .rbenv`
* Add the `rbenv` binaries to your `PATH` (`PATH` is where your Linux distribution will look for applications to run): `echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc`
* Set up `rbenv` to be set up when you log in: `echo 'eval "$(rbenv init -)"' >> ~/.bashrc`.
* Log out of your SSH session and log back in to ensure rbenv has been loaded.
* Install the `ruby-build` rbenv plugin: `mkdir -p ~/.rbenv/plugins/` and `git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build`
* Install Ruby: `rbenv install 2.0.0-p0` and tell rbenv to reload: `rbenv rehash`. 
* Finally, set this version of Ruby we just installed as the default, system Ruby: `rbenv global 2.0.0-p0`.


If you've followed one of the steps above - congratulations, Ruby is installed. As said, the method you have installed with doesn't really matter - it's really based on your preference, and what's important to you. Now that we've got Ruby installed though, we can carry on with our server set up!

**Next:** [[Setting up core gems|bundler]]