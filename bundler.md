Installing Bundler
===

At this point, the server itself is largely set up - well done! Now that all of the system-level tools and applciations are in place, it's time to prepare our local user environment to run our application. The centerpiece of that environment, the bit that holds it all together, is [bundler](http://gembundler.com/).

The bundler website, or it's README will set out much more clearly how it works, and why it's necessary - it's enough for us to know that it allows us to _declare_ the Rubygems that our application needs to use, and then _install_ them where necessary - in our case, our server. If you're using Rails 3 and above, you almost certainly will have used bundler during development, probably editing your `Gemfile` and running `bundle install`.

When you use `bundler` during development, you're doing the 'declaration' part. Having bundler installed on the server means that the dependencies for the application can be installed in a clean, reliable, and self-contained way. This is because `bundler`, when running on a server, packages all the files that make up the gems installed into a folder that becomes part of the application folder on the server - so there is no opportunity for gems from diferent projects and of different versions to conflict.

> In case you need to know, this folder is, by default, in the 'bundle' folder of your Capistrano deploy's 'shared' folder - we'll deal with this in the 'Deployment' section.


> If you like the idea of this seperation of gems into individual projects, you can set up your own development environment to work in the same way - just run `bundle config --global path .bundle` on your laptop or workstation.

Now that you know about what `bundler` does, and why it's useful for us, it's actually really easy to install - we can install using the `gem` command. Just before we do this though, let's go over the hierarchy of gems in 'RVM', 'rbenv', and 'system'.

#### RVM

RVM is the most complex version manager, and as such, there's a number of levels where you might install a gem.

1. **Installed against a Ruby:** this is equivalent to installing a gem without RVM installed - the gem is recorded against the current Ruby version being used, and is accessible to anything that also uses that Ruby version. For example, if you had an application named `facebook`, and an application named `github`, and you installed a gem named `party`, both `facebook`, and `github` would be able to access `party`.
2. **Installed in a global gemset:** this is the next level down, but is actually equivalent to installing against a Ruby - it's just the 'RVM way' of doing it. RVM's support for gemsets are one of the big advantages of using the framework, as they're a great way to divide up gems you require for different purposes. Sometimes, though, you'll have a gem that is quite universal, and should be available in all your current gemsets, as well as any you create - this is what the 'global' gemset is for. When a regular RVM gemset is called `1.9.3@test`, the global gemset can be used using the version string `1.9.3@global`.
3. **Installed in a gemset:** this is the lowest level that you can install gems with RVM - effectively, this installs the gem in a 'jail' of sorts, that have to specifically request to access. Generally, gemsets might be used for projects where there are complex dependencies that are not catered for by bundler, or if it is a legacy application. Be careful about installing too many gems in different gemsets as you need them, because over time you might end up with different versions of gems in different gemsets, which can lead to confusion.

Given these three options then, which is the best for installing `bundler`? Since it's a very high-level and important tool for us, I recommend you install it directly against the Ruby you are going to be using. While RVM's gemset support is very cool, you might not end up using it as much as you think, or at all, so installing against the Ruby will ensure that Bundler is accesible to us.

#### rbenv

rbenv does actually have support for gemsets, but it's via a plugin. The maintainers of rbenv do not believe gemset support is important in the main codebase, as, if used correctly, Bundler itself can perform a similar role.

Because of this decision, there's really only one place we can install Bundler - against the Ruby version we will be using. This way, the gem will always be available to use in our projects that are using the same version of Ruby.

#### Ruby installed from source

This one should be even more obvious - if you have installed Ruby from source, then you don't have gemsets, or even multiple versions of Ruby - you can only install gems directly against the Ruby version. This isn't actually a downside though, as typically you will have only a small number of 'global' gems - all the rest will be tucked out of the way by Bundler.

> If you're not using Bundler, and plan to have more than one application hosted on this server, and you have installed Ruby from source, I strongly recommend you reconsider - without Bundler and multiple versions of Ruby, all installed gems go into one big pile, which can cause real headaches with version conflicts and obscure dependency breakages.


Whichever method we have installed Ruby with, the general flow is the same - it's the 'scope' we're in that's important. If you're using RVM, this scope will be out of any gemsets, in the main Ruby you will be using. If you're using rbenv or Ruby from source, the scope will kind of be decided for you by whatever version of Ruby you're using.

RVM users, you can make sure you're in the right scope by typing `rvm use [your-ruby-version]` - for example, `rvm use 1.9.3`.

rbenv users, you should already be in the right scope, but you can check which version of Ruby you're using currently by typing `rbenv version`.

Ruby from source, you're good to go - you're mono-scopic.

When we install gems, we use a utility called [Rubygems](https://rubygems.org), which is a bit like Debian's `apt-get`, or Red Hat's `yum` - it's a way of asking for something to be installed, at a particular version, including all dependencies. The gem downloads all the necessary files (which are actually packaged within the '.gem' file that the `gem` program will download for us), and moves them into place - it sounds simple, but it's not - always. Luckily for us, we're installing a very standard, and well supported tool - let's do it!

Run the following command to install Bundler:

``` bash
gem install bundler --no-rdoc --no-ri
```

You should see some output about 'Fetching bundler-x.x.x.gem', and then see something like 'Bundler x.x.x installed.' **rbenv users, you should run `rbenv rehash` now to update rbenv**. To make sure it's installed, try running `bundle help` to bring up the help doc for Bundler - have a read, and hit 'q' when you're ready to carry on to deploying your application.

In this section, we covered a lot of the foundations about how gems are installed and loaded, and the best place to put things on your server when you're installing global, system-level gems. In the next section, we're going to cover how to actually deploy your application using `bundler` and `Capistrano`.

**Next:** [[Deploying your Application|deployment]]
