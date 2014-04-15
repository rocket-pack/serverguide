PostgreSQL is a relational database engine preferred over MySQL due to it's true open-source, speed of updates, and features/extensions such as Postgis.

The latest version takes a long time to get to Ubuntu, so it's best to add the APT repository from PostgreSQL directly.

### Add the APT repository:

Follow the instructions on the PostgreSQL APT wiki page: https://wiki.postgresql.org/wiki/Apt.

Once the apt repository is added, the latest version of postgres can be installed:

``` bash
sudo apt-get install postgresql-9.3 libpq-dev
```

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



