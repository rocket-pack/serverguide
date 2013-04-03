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

---



Install the database packages
``` bash
sudo apt-get install libpq-dev
sudo add-apt-repository ppa:pitti/postgresql
sudo apt-get update
sudo apt-get install postgresql-9.2
```

### Setup User

login as the default postgres user
``` bash
sudo -u postgres psql
```

Create a user for your application (change $password to a real password and dogbook with your application name):
postgres=# ``` CREATE USER dogbook WITH PASSWORD '$password'; ```

Quit the database session
postgres=# ``` \q ```

### Port

To check that the default port of ``` 5432 ``` is correctly set run the following command:
``` bash
sudo netstat -lp | grep postgresql
```

Reset to default port or change port if necessary
``` bash
sudo vim /etc/postgresql/9.2/main/postgresql.conf
```

Change the port value in the conf file
```
port = 5432
```

Restart postgres to pick up the new configuration changes
``` bash
sudo /etc/init.d/postgresql reload
```
