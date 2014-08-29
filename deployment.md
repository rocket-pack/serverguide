> Note: This page is a work-in-progress. The rough steps you need to follow are listed below, but may not be correctly ordered or have enough detail to get through.

* Checkout feature branch for deploy - feature/deploy suggested
* Open Gemfile - add capistrano in development group
* Bundle
* `bundle exec cap install`
* Have a read of `Capfile`, `config/deploy.rb`, `config/deploy/*`
* Uncomment required gems in Capfile - by default, we'll need `capistrano/bundler` and `capistrano/rails` - add `capistrano-bundler` and `capistrano-rails` to `Gemfile`
* If you installed Ruby with `rbenv`, also add the `capistrano/rbenv`/`capistrano-rbenv` support
* Check out the docs for each project so xyou can see the config you'll need to set
* Open `Capfile` for global config:
	* Add in your project's SCM url
	* Add `set :ssh_options, { :forward_agent => true }`
	* Add linked files 
	* Uncomment linked dirs
	* Uncomment touch restart line
	* Put in rbenv config from project readme
	* Ensure ruby version is correct (from the version of Ruby installed on your server)
	* Add `server 'server ip', user: '[application user]', roles: %w(web app db)`
)
	* Open deploy/staging.rb, remove all lines and replace with:
		* `set :deploy_to, '/home/[application username]/apps/[application_name]_staging`
	* Do the same for production.rb, replacing the `_staging` bit with nothing, so it's just [application name]
* Close all other connections
* SSH into the server using the `-A` flag (this is important!)and run `ssh git@github.com` - you should be greeted with your Github username after being prompted to accept the host key to connect to Github.
* Run `cap staging deploy`. At this point many things can go wrong, so follow the errors as best as you can.


* Go into /etc/apache2/sites-available, and launch an editor for your staging site: `sudo vim [app-name]-staging.[domain].com`. Paste in the default 