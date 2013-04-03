### Install PostgreSQL

PostgreSQL is a mature and capable relational database system. If you're looking to deploy a Ruby on Rails application, then PostgreSQL is a great choice. This section of the server guide is going to cover installing and configuring PostgreSQL, and then configuring your application to use PG as the data store.

> There has always been fierce debate over the various advantages and disadvantages of PostgreSQL and MySQL. In this example, we use PostgreSQL because it has a strong community, a history of transparent releases (it is open source), and good support for direct server work.

Before we get started, Ubuntu 12.04 users have a little more work to do, as the latest versions of PostgreSQL are no longer released for this version of Ubuntu, so we need to add a "PPA" (Personal Package Archive). A PPA allows us to install newer or different packages than those in the official repositories - in this case, it allows us to install PostgreSQL 9, which a user named 'pitti' has backported to work on 12.04. 

You can add the PPA by running the following command, and then running a package update to fetch the latest info about available packages:

``` bash
sudo apt-add-repository ppa:pitti/postgresql
sudo apt-get update
```

> If you'd like to find out more about the ramifications of adding a PPA to a server, have a read of [this StackOverflow question](http://askubuntu.com/questions/35629/are-ppas-safe-to-add-to-my-system-and-what-are-some-red-flags-to-watch-out).

If you're using Ubuntu 12.10 or later, the packages we are about to install are already in the default Ubuntu repositories, so we don't need to add anything - we can just install PostgreSQL!

To install the database server, we need to tell `apt-get` to download and install a two packages - one being the database server itself, and the other being a development library that Rails will require to talk to the PostgreSQL database.

Here's the command to install PostgreSQL:

``` bash
sudo apt-get install postgresql-9.2 libpq-dev
```

Once this command has completed (which may take some time), you should have PostgeSQL installed and running (you may have seen some messages while it installed about starting the database server and setting up clusters). By default, PostgreSQL runs on port number '5432' - to prevent confusion when we begin trying to connect to our database, let's run a quick command to make sure that PostgreSQL is indeed running and listening on that port:


``` bash
sudo netstat -lp | grep postgresql
```

> If the command above doesn't print anything out, it's possible that PostgreSQL is running on a different port. You should be able to fix this by opening /etc/postgresql/9.2/main/postgresql.conf and checking the value of the 'port' configuration setting. If this isn't set to '5432', set it, save the file, and restart the database server by running `sudo /etc/init.d/postgresql restart`.

### Setup User

login as the default postgres user
``` bash
sudo -u postgres psql
```

Create a user for your application (change $password to a real password and dogbook with your application name):
postgres=# ``` CREATE USER dogbook WITH PASSWORD '$password'; ```

Quit the database session
postgres=# ``` \q ```

