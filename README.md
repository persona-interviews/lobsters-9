### Lobsters Rails Project [![build status](https://github.com/lobsters/lobsters/actions/workflows/check.yml/badge.svg)](https://github.com/lobsters/lobsters/actions/workflows/check.yml)

This is a Hacker News clone used at [https://lobste.rs](https://lobste.rs).
It is a Rails codebase and uses a SQL (MariaDB in production) backend for the database.

You are free to use this code to start your own [sister site](https://github.com/lobsters/lobsters/wiki)
because the code is available under a [permissive license](https://github.com/lobsters/lobsters/blob/master/LICENSE) (3-clause BSD).
We welcome bug reports and code contributions that help use improve [lobste.rs](https://lobste.rs).
As a volunteer project we're reluctant to take on work that's not useful to our site, so please understand if we don't want to adopt your custom feature.


#### Contributing bugfixes and new features

We'd love to have your help.
Please see the [CONTRIBUTING](https://github.com/lobsters/lobsters/blob/master/CONTRIBUTING.md) file for details.
If you have questions, there is usually someone in [our chat room](https://lobste.rs/chat) who's familiar with the code.


#### Development setup

Use the steps below to setup development or use
[lobsters-ansible](https://github.com/lobsters/lobsters-ansible) for our production deployment config.
There's an external project [docker-lobsters](https://github.com/utensils/docker-lobsters) if you want to use Docker.

* Create a Github Codespace by changing the `.com` in the URL to `.dev`. New URL will be `https://github.dev/persona-interviews/lobsters`
    * Click the green button at the bottom labeled "Continue Working in Github Codespaces"
    * Select "2 cores..." at the top

* Install and start MariaDB.
    ```
    $ sudo apt-get update
    $ sudo apt-get install mariadb-server
    ```

* Start the mariadb server
    ```
    $ sudo service mysql start
    ```

* Open the console using `sudo mysql -u root`, and set the `root` user password (type `ctrl-d` to exit afterwards)
    ```sql
    ALTER USER 'root'@'localhost' IDENTIFIED BY 'localdev';
    ```

* Install the Ruby version specified in [.ruby-version](https://github.com/lobsters/lobsters/blob/master/.ruby-version)
    ```
    lobsters$ rvm install ruby 3.3.1
    ```

* Install Nodejs, needed (or other execjs) for uglifier
    ```sh
    $ sudo apt-get install nodejs
    ```

* Run `bin/setup` to install dependencies and set up db
    ```sh
    lobsters$ bin/setup
    ```

* On your production server, copy `config/initializers/production.rb.sample`
  to `config/initalizers/production.rb` and customize it with your site's
  `domain` and `name`. (You don't need this on your dev machine).

* On your personal computer, you probably want to add some sample data.
    ```sh
    lobsters$ rails fake_data
    ```

* Run the Rails server in development mode.
  You should be able to login to `http://localhost:3000` with your new `test` user:
    ```sh
    lobsters$ rails server
    ```

* Deploying the site in production requires setting up a web server and running the app in production mode.
  There are more tools and options available than we can describe; find a guide or an expert.
  The lobsters-ansible repo has our config files to crib from. Some app-specific notes:

* Set up crontab or another scheduler to run regular jobs:
    ```
    */5 * * * *  cd /path/to/lobsters && env RAILS_ENV=production sh -c 'bundle exec ruby script/mail_new_activity; bundle exec ruby script/mastodon_sync.rb; bundle exec ruby script/traffic_range'
    ```

#### Administration

Basic moderation happens on-site, but most other administrative tasks require use of the rails console in production.
Administrators can create and edit tags at `/tags`.
