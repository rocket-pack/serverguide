PostgreSQL is a relational database engine preferred over MySQL due to it's true open-source, speed of updates, and features/extensions such as Postgis.

The latest version takes a long time to get to Ubuntu, so it's best to add the APT repository from PostgreSQL directly.

### Add the APT repository:

> Note: These instructions were derived from the PostgreSQL APT wiki page - check there for details: https://wiki.postgresql.org/wiki/Apt.

You'll be adding a custom APT repository to install Postgres directly from the supplier - to do that, you need to add a '.list' file listing where the software can be found. Many APT repositories package their software for different versions of Debian, which Ubuntu is based upon, so you'll need your release name to add the correct repository data. 

You can get your release name by running the following command:

``` bash
lsb_release -c
```

This should return a single-word release for your installation - for example, Ubuntun 12.04 will return 'precise'.

Copy this release name, and open vim in a new .list file by running the following command:

``` bash
sudo vim /etc/apt/sources.list.d/pgdg.list
```

Hit 'i' to go into insert mode, and paste in the following source:

```
deb http://apt.postgresql.org/pub/repos/apt/ [RELEASE NAME]-pgdg main
```

Replace [RELEASE NAME] with your release name you copied above, then hit <kbd>Esc</kbd>, and then type <kbd>:x</kbd> to write the file and exit.

Now that the file is in place, we can update our APT sources and install Postgres from the latest release. First, run `apt-get update`:

``` bash
sudo apt-get update
```

Then run the install process:

``` bash
sudo apt-get install postgresql-9.3 libpg-dev
```

Once this is done, Postgres will be installed and running. You might be able to spot it in the process list by running `htop`.


### Set up application database user

If you're connecting to the database from the local host, then you can do so without requiring a password. The only thing you need to do to accomplish this is have a Postgres role name matching the name of a local system user. 

When you installed Postgres, a number of helpful utilities were also installed:

* `createuser` - for creating users in Postgres (not to be confused with `adduser`, which will add users to the system itself)
* `deluser` - for delting users from Postgres
* `createdb` - for creating databases in Postgres. You should generally always use the `--encoding=unicode` and `--owner=[application user]` flags for this command.
* `dropdb` - for deleting databases.

Another thing you got when you installed was a `postgres` user. You can `sudo su postgres` to change to this user and have superuser access to the database. 

To create the application user, you can use the `createuser` command. You can just answer 'no' to all the prompts - your app user doesn't need any special access:

``` bash
createuser [application account name, e.g. dogbook]
```

> Note: The low level of priviledge given to this role means you will not be able to use some `rake` tasks, particularly those that drop and re-create the database. You will have to do these operations by jumping into the `postgres` account and using the utilities described above.


Once your user exists, you can create the databases you'll need. Your specific database names depend on your app and if you want a staging application on the same server, but generally your databases should be named after the Rails environment names they will be run in, for example:

``` bash
createdb [app name]_production --owner=[application account name] --encoding=unicode
```
Repeat this command if you require another database for staging - you might want to run this application in the `draft` Rails environment, so you should name your database accordingly.

---

Once you've performed these tasks, the setup of PostgreSQL is complete. You should be able to connect to your Rails application database from your local account now - make sure you're logged in as your application user again (hint: if you're still logged in as postgres from running `sudo su postgres`, you can just type `exit` to jump back to your app user), and run `psql [your database name]` - you should be dropped into a database console without any access issues. You can exit this console by hitting `\q`.

If you _did_ have access issues, go back and check your database user is named the same as your app user, and that the databases you need were created correctly with the `--owner=[app user]` flag.

