Deployinator
============

Deploy with style!


Stacks
------

Deployments are grouped by "stacks". You might have a "web" and "search" stack.

Each of those stacks might have different deployment environments, such as "staging" or "production".


Install
-------

Deployinator is a standard [Rack][1] app (mostly [Sinatra][2]).
Point your rack-capable web server at the `config.ru`, and it should mostly work. All dependencies are managed with [bundler][3], so a `bundle install` should get you set up mostly.

It has been tested with ruby 1.8.6, 1.8.7 and 1.9.2. For local development, you can use [Pow!](http://pow.cx/). Assuming you are using OSX, you'd want to do the following:

* `curl get.pow.cx | sh`
* `cd ~/.pow`
* `ln -s /path/to/deployinator`
* `cd /path/to/deployinator`
* `bundle install`
* `echo "export HTTP_X_USERNAME=$USER\nexport HTTP_X_GROUPS=foo" > .powenv`
* visit [deployinator.dev](http://deployinator.dev) in your browser

If you are using RVM you may need to echo the ruby version like so:

* `echo "rvm ruby-1.9.3-head" > .rvmrc`


Authentication
----------------

At Etsy, we use an internal SSO similar to [GodAuth][4], which sets http headers that are checked in deployinator.

This code is abstractable, and will be made more generic soon.

There are a list of urls that don't require authentication, those are currently defined at the top of `helpers.rb` but should be either set in a config, or maybe a sinatra plugin?


Creating a new stack
--------------------

To create a new stack, run the rake task "new_stack"

    STACK=my_blog rake new_stack


Configuration
-------------

Configuration settings live in config/*.rb. For example, your config.development.rb might look like this:

```
Deployinator.log_file = Deployinator.root(["log", "development.log"])
Deployinator.domain = "awesomeco.com"
Deployinator.hostname = "deployinator.dev"
Deployinator.graphite_host = "localhost"
Deployinator.github_host = "github.com"
Deployinator.default_user = "joboblee"

Deployinator.new_relic_options = {
  :apikey => "12345",
  :appid  => "4567"
}

Pony.options = {
  :via         => :smtp,
  :from        => "deployinator@#{Deployinator.domain}",
  :headers     => {"List-ID" => "deploy-announce"},
  :to          => "joboblee@#{Deployinator.domain}",
  :via_options => {
    :address              => 'smtp.gmail.com',
    :port                 => '587',
    :enable_starttls_auto => true,
    :user_name            => 'gmailuser@gmail.com',
    :password             => 'gmail-password',
    :authentication       => :plain,
    :domain               => Deployinator.domain
  }
}

```

These options are documented near the top of libraries.rb

[1]: http://rack.rubyforge.org/
[2]: http://www.sinatrarb.com/
[3]: http://gembundler.com/
[4]: https://github.com/exflickr/GodAuth

