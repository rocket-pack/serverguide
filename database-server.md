### Install the database packages
``` bash
sudo apt-get install libpq-dev
sudo add-apt-repository ppa:pitti/postgresql
sudo apt-get update
sudo apt-get install postgresql-9.2
```

### login as the default postgres user
sudo -u postgres psql

### Create a user for your application (change $password to a real password and dogbook with your application name):
postgres=# CREATE USER dogbook WITH PASSWORD '$password';

### Quit the database session
postgres=# \q