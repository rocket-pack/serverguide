Install memcached via apt-get.
``` bash 
sudo apt-get install memcached
```

Confirm memcached install.
You can check memcached has been installed and running by running the following:
``` bash
ps aux | grep memcache
```

To check out memcache stats.
``` bash
echo "stats settings" | nc localhost 11211
```