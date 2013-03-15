# Install the database packages
sudo apt-get install -y postgresql-9.1 libpq-dev

# Login to PostgreSQL
sudo -u postgres psql -d template1

# Create a user for your application (change $password to a real password and dogbook with your application name):
template1=# CREATE USER dogbook WITH PASSWORD '$password';

# Quit the database session
template1=# \q