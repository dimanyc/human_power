[![Build Status](https://secure.travis-ci.org/lassebunk/human_power.png)](http://travis-ci.org/lassebunk/human_power)

# Human Power

Human Power lets you write your robots.txt in plain Ruby and force the robots into submission!

![Bingbot getting taught](http://i.imgur.com/77WVSQH.jpg)

## Installation

Add this line to your *Gemfile*:

    gem 'human_power'

Then run:

    $ bundle

Or install it yourself:

    $ gem install human_power

If you are using Rails, you can add a sample *config/robots.rb* configuration file and route for `/robots.txt`:

    $ rails g human_power:install

It will allow crawlers access to the whole site by default.

Now you can restart your server and visit `/robots.txt` to see what's generated from the new configuration file. If you are running Rails 4.*, make sure you remove the static `robots.txt` file residing in `/public`

## Usage

### Using in Ruby on Rails

In *config/robots.rb*:

```ruby
# Disallow everything in /admin for all user agents
disallow_tree admin_path

# Googlebot
googlebot do
  disallow reviews_path # Disallow a path
  allow product_path("one") # Allow a path
  disallow new_product_path, new_category_path # Disallow multiple paths in one line
end

# Bingbot
bingbot do
  disallow :all # There you go, Bingbot! (Disallow everything)
end

# Identical settings for multiple user agents
user_agent [:bingbot, :googlebot] do
  disallow login_path
end

# Custom user agent
user_agent "My Custom User Agent String" do
  disallow some_path
end

# You have access to everything from your ApplicationController
if request.subdomains.first == "api"
  disallow :all
end

# Add one or more sitemaps
sitemap sitemap_url
sitemap one_url, two_url
```

Then visit `/robots.txt` in your browser.

## Crawlers

Please see [user_agents.yml](https://github.com/lassebunk/human_power/blob/master/user_agents.yml) for a list of 170+ built-in user agents/crawlers you can use like shown above.
The list is from [UserAgentString.com](http://www.useragentstring.com/pages/Crawlerlist/).

### Bot detection

You can use the `HumanPower.is_bot?` method to check if a user agent is a known bot / crawler:

```ruby
# Googlebot
ua = "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)"
HumanPower.is_bot?(ua) # => true

# Chrome
ua = "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1944.0 Safari/537.36"
HumanPower.is_bot?(ua) # => false

# in Rails
HumanPower.is_bot?(request.user_agent) # => performs check on current user agent
```

### Regular expression

If you need to get a regular expression for bot detection, you can use:

```ruby
HumanPower.bot_regex # => regular expression that matches all known bots / crawlers
```

## Caveats

Human Power is great for adding rules to your robots.txt.
You should note, however, that the user agents are sorted alphabetically upon rendering.
This is fine for most use cases, but if you add more advanced rules relying on user agent
order, please be sure to check that robots.txt is generated into something that meets
your needs. If you need more advanced features and rely heavily on ordering, please submit
an [issue](https://github.com/lassebunk/human_power/issues)
or [pull request](https://github.com/lassebunk/human_power/pulls). Thanks.

## Versioning

Follows [semantic versioning](http://semver.org/).

## Contributing

You are very welcome to contribute to this project if you have a feature that you think others can use. I'd appreciate it.

1. Fork the project
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new pull request
