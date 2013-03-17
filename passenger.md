Once you have ruby on the server, we can install passenger via a handy gem: 
``` bash
gem install passenger
```

Once the gem has been downloaded, run this command to start the passenger install process:
``` bash
passenger-install-apache2-module
```
Follow the onscreen instructions during the install and once passenger has successfully installed its time to update our apache config as per the post install message.

Copy and paste the lines from the output into your apache config:
``` bash
vim /etc/apache2/apache2.conf
```