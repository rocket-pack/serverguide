Before we install passenger, lets first install apache2 if its not already on the server:
``` bash
sudo apt-get install apache2 apache2-mpm-prefork apache2-prefork-dev
```

Once you have ruby on the server, we can install passenger via a handy gem: 
``` bash
gem install passenger
```

Once the gem has been downloaded, run this command to start the passenger install process:
``` bash
passenger-install-apache2-module
```

If that does not work, make sure you have run `rbenv rehash`.

Follow the onscreen instructions during the install and once passenger has successfully installed its time to update our apache config as per the post install message.

Copy and paste the lines from the output into your apache config:
``` bash
vim /etc/apache2/apache2.conf
```

Enable mod_rewrite for apache2:
``` bash 
sudo a2enmod rewrite
```

Create a file in ``` /etc/apache2/sites-available/ ``` for your site, for example ``` dogbook.co.nz ```:
``` bash
<VirtualHost *:80>
    # Change these 3 lines to suit your project
    RailsEnv production
    ServerName dogbook.co.nz
    DocumentRoot /home/dogbook/apps/dogbook/public
</VirtualHost>
```

Enable our new site:
``` bash
sudo a2ensite dogbook.co.nz
sudo /etc/init.d/apache2 restart
```