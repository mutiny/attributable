# Attributable [![Build Status](https://travis-ci.org/louismrose/mutiny.png?branch=master)](https://travis-ci.org/louismrose/mutiny) [![Code Climate](https://codeclimate.com/github/louismrose/mutiny.png)](https://codeclimate.com/github/louismrose/mutiny) [![Dependency Status](https://gemnasium.com/louismrose/mutiny.png)](https://gemnasium.com/louismrose/mutiny) [![Coverage Status](https://coveralls.io/repos/louismrose/mutiny/badge.png?branch=master)](https://coveralls.io/r/louismrose/mutiny?branch=master)

A tiny library that makes it easy to create value objects.

## Basic usage

    require "attributable"

    class User
      extend Attributable
  
      attributes :forename, :surname
    end

    john = User.new(forename: "John", surname: "Doe")
    john.forename # => "John"
    john.surname  # => "Doe"

All attributes are read-only:

    john.forename = "Jonathan" # => NoMethodError: undefined method `forename='

Default values for attributes can be set via a hash argument to `attributes`:

    class UserWithDefaults
      extend Attributable

      attributes :forename, :surname, active: true
    end
    
    anon = UserWithDefaults.new
    anon.active   # => true
    anon.forename # => nil

## Equality

Attributable adds `eql?` and `==` methods to your class which compare attribute values and types.

    john = User.new(forename: "John", surname: "Doe")
    second_john = User.new(forename: "John", surname: "Doe")

    john.eql? second_john # => true
    john == second_john   # => true
  
The equality methods return false when compared to an object of the same type with different attribute values:

    jane = User.new(forename: "Jane", surname: "Doe")
    john.eql? jane # => false
    john == jane   # => false
  
The equality methods return false when compared to an object of a different type, even if the attribute values are equal.

    class Admin
      extend Attributable

      attributes :forename, :surname
    end

    admin_john = Admin.new(forename: "Jane", surname: "Doe")
    john.eql? admin_john # => false
    john == admin_john   # => false

Because Attributable overrides `eql?` and `==`, it also overrides `hash`:

    john.hash == second_john.hash # => true
    john.hash == jane.hash        # => false
    john.hash == admin_john.hash  # => false

## Pretty printing

Attributable adds an `inspect` method to your class which display attribute values.

    john.inspect # => <User forename="John", surname="Doe">

## Specialisation

To allow reuse of attribute declarations, Attributable provides the `specialises` class method.

    class Author
      extend Attributable
      
      specialises User
      attributes blogs: []
    end
    
    ronson = Author.new(forename: "Jon", surname: "Ronson")
    ronson.inspect # => <Author forename="Jon", surname="Ronson", blogs=[]>
    
Specialising classes can override the defaults set in specialised classes.

    class Ronson
      extend Attributable

      specialises User
      attributes surname: "Ronson"
    end

    Ronson.new(forename: "Jon").inspect # <Ronson forename="Jon", surname="Ronson">
    Ronson.new(forename: "Mark").inspect # <Ronson forename="Mark", surname="Ronson">

## Installation

Add this line to your application's Gemfile:

    gem 'attributable'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install attributable

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
