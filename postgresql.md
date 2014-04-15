PostgreSQL is a relational database engine preferred over MySQL due to it's true open-source, speed of updates, and features/extensions such as Postgis.

The latest version takes a long time to get to Ubuntu, so it's best to add the APT repository from PostgreSQL directly.

### Add the APT repository:

Follow the instructions on the PostgreSQL APT wiki page: https://wiki.postgresql.org/wiki/Apt.

Once the apt repository is added, the latest version of postgres can be installed:

``` bash
sudo apt-get install postgresql-9.3 libpq-dev
```
