# Reactstrap Sample

This repository is an instructional example for creating a Rails application
using Webpacker, React.js and Reacstrap. It's based on Rails 5.2.2 running on
Ruby 2.5.1

# Setup

To setup this application we did the following which includes installation and
updating of the javascript packages:

1. `rails new reactstrap-rails --webpack=react --skip-coffee --skip-javascript`
1. `cd reactstrap-rails`
1. `yarn upgrade @rails/webpacker@^4.0`
1. `yarn upgrade webpack-dev-server@^3.2`
1. `yarn add reactstrap`
1. `yarn check` - should not yield any warnings

# Initial Controller and View Setup

1. Edit Gemfile to down grade Sqlite3 from 1.4.x since Rails doesn't support it yet.
   ```
   gem 'sqlite3', '~> 1.3.0'
   bundle update
   ```
1.  Create the home controller.
   ```
   rails g controller Home index
   ```
1. Add the root route to the config/routes.rb file.
   ```
   root to: 'home#index'
   ```
1. Update the view template app/views/layouts/application.html.erb by adding:
   ```
   <%= javascript_pack_tag 'hello_react' %>
   ```
1. Update config/initializers/content_security_policy.rb to allow content from
   webpack-dev-server.
   ```
   policy.connect_src :self, :https, 'http://localhost:3035', 'ws://localhost:3035' if Rails.env.development?
   ```
