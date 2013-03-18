### Install PostgreSQL

Install the database packages
``` bash
sudo apt-get install libpq-dev
sudo add-apt-repository ppa:pitti/postgresql
sudo apt-get update
sudo apt-get install postgresql-9.2
```

### Setup User and Database

login as the default postgres user
``` bash
sudo -u postgres psql
```

Create a user for your application (change $password to a real password and dogbook with your application name):
postgres=# ``` CREATE USER dogbook WITH PASSWORD '$password'; ```

Quit the database session
postgres=# ``` \q ```

To check that the default port of ``` 5432 ``` is correctly set run the following command:
``` bash
sudo netstat -lp | grep postgresql
```

Reset to default port (or change port if necessary)
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
