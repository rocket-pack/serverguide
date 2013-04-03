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

With the database server installed, we're almost all set to configure our Rails application to connect and use PostgreSQL to store our data. The last thing we need to do is create a 'user' in the database server. This user is different to the user that we set up when we were first getting our server set up (although it can have the same name) - it's just to access the database. 

We're going to set up a 'passwordless' user account in PostgreSQL that should let us in without any problems as long as we are logged in as our application user on the server. 

> A passwordless user account may not be the safest option for you if you are going to be storing a lot of sensitive data in your application. For most needs though, it provides a way of authenticating with the database that is quick, easy, and just works. If you _would_ like to set a password for you user, just can just run the following command once you've created the user account as outlined below: `psql -c "ALTER USER [application  user] WITH ENCRYPTED PASSWORD '[your secure password]';"`.

To create the user account, we first need to change the user account we are logged in as - right now, we're logged in as the application user that we created when we were first setting up our server, but we need to now be logged in as a user called "postgres" - this is a special user account that automatically has top-level access to the PostgreSQL database server. 

Because we can already use `sudo`, we can also use `su`, to switch users, using the following command:

``` bash
sudo su postgres
```

You should notice that the 'prompt' (the text that shows before you enter a command) has changed - it should now read "postgres@your-hostname" - this means you are logged in as the "postgres" user. 

Once you are logged in as Postgres, you need to issue a single command to create the user account we will use in our Rails app to connect. Let's set the name of this user account to be the same as our application account (for example, 'dogbook'). Here's the command you need to run:

``` bash
createuser [your appliction name]
```

You'll be prompted for three levels of permission, and you should answer 'No' for each - the fewer priviledges this user account has, the better. 

Once this command has finished, you can type `exit` to log out of the 'postgres' user account and return to your application user. To check that the database user account is set up correctly, try running this command:

``` bash
psql
```

If this returns the error "psql: FATAL:  database [user name] does not exist" - it's working correctly - we just haven't created a database for our application yet. If it returns a different error, or asks for a password, you may want to re-check how this user is set up.
