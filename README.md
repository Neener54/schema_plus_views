[![Gem Version](https://badge.fury.io/rb/schema_plus_views.svg)](http://badge.fury.io/rb/schema_plus_views)
[![Build Status](https://secure.travis-ci.org/SchemaPlus/schema_plus_views.svg)](http://travis-ci.org/SchemaPlus/schema_plus_views)
[![Coverage Status](https://img.shields.io/coveralls/SchemaPlus/schema_plus_views.svg)](https://coveralls.io/r/SchemaPlus/schema_plus_views)
[![Dependency Status](https://gemnasium.com/lomba/schema_plus_views.svg)](https://gemnasium.com/SchemaPlus/schema_plus_views)

# SchemaPlus::Views

SchemaPlus::Views adds support for creating and dropping views in ActiveRecord migrations, as well as querying views.

SchemaPlus::Views is part of the [SchemaPlus](https://github.com/SchemaPlus/) family of Ruby on Rails extension gems.

## Installation

<!-- SCHEMA_DEV: TEMPLATE INSTALLATION - begin -->
<!-- These lines are auto-inserted from a schema_dev template -->
As usual:

```ruby
gem "schema_plus_views"                # in a Gemfile
gem.add_dependency "schema_plus_views" # in a .gemspec
```

To use with a rails app, also include

```ruby
gem "schema_monkey_rails"
```

which creates a Railtie to that will insert SchemaPlus::Views appropriately into the rails stack. To use with Padrino, see [schema_monkey_padrino](https://github.com/SchemaPlus/schema_monkey_padrino).

<!-- SCHEMA_DEV: TEMPLATE INSTALLATION - end -->

## Compatibility

SchemaPlus::Views is tested on:

<!-- SCHEMA_DEV: MATRIX - begin -->
<!-- These lines are auto-generated by schema_dev based on schema_dev.yml -->
* ruby **2.1.5** with activerecord **4.2**, using **mysql2**, **sqlite3** or **postgresql**

<!-- SCHEMA_DEV: MATRIX - end -->

## Usage

### Creating views

  In a migration, a view can be created using literal SQL: 

```ruby
create_view :uncommented_posts,        "SELECT * FROM posts LEFT OUTER JOIN comments ON comments.post_id = posts.id WHERE comments.id IS NULL"
```

or using an object that responds to `:to_sql`, such as a relation:

```ruby
create_view :posts_commented_by_staff,  Post.joins(comment: user).where(users: {role: 'staff'}).uniq
```

(It's of course a questionable idea for your migrations to depend on your model definitions.  But you *can* if you want.)

### Dropping views

In a migration:

```ruby
drop_view :posts_commented_by_staff
drop_view :uncommented_posts, :if_exists => true
```

### Using views

ActiveRecord models can be use views the same as ordinary tables.  That is, for the above views you can define

```ruby
class UncommentedPost < ActiveRecord::Base
end
    
class PostCommentedByStaff < ActiveRecord::Base
  table_name = "posts_commented_by_staff"
end
```

### Querying views

You can look up the defined views analogously to looking up tables:

```ruby
connection.tables # => array of table names (defined by ActiveRecord)
connection.views  # => array of names of views (defined by SchemaPlus::Views)
```

Notes:

1. For Mysql and SQLite3, ActiveRecord's `connection.tables` method would return views as well as tables; SchemaPlus::Views normalizes them to return only tables.

2. For PostgreSQL, `connection.views` does *not* return views prefixed with `pg_` as those are presumed to be internal.

### Querying view definitions

You can look up the definition of a view using

```ruby
connection.view_definition(view_name) # => returns SQL string
```

This returns just the body of the definition, i.e. the part after the `CREATE VIEW 'name' AS` command.


## History

* 0.1.0 - Initial release, extracted from schema_plus 1.x

## Development & Testing

Are you interested in contributing to SchemaPlus::Views?  Thanks!  Please follow
the standard protocol: fork, feature branch, develop, push, and issue pull
request.

Some things to know about to help you develop and test:

<!-- SCHEMA_DEV: TEMPLATE USES SCHEMA_DEV - begin -->
<!-- These lines are auto-inserted from a schema_dev template -->
* **schema_dev**:  SchemaPlus::Views uses [schema_dev](https://github.com/SchemaPlus/schema_dev) to
  facilitate running rspec tests on the matrix of ruby, activerecord, and database
  versions that the gem supports, both locally and on
  [travis-ci](http://travis-ci.org/SchemaPlus/schema_plus_views)

  To to run rspec locally on the full matrix, do:

        $ schema_dev bundle install
        $ schema_dev rspec

  You can also run on just one configuration at a time;  For info, see `schema_dev --help` or the [schema_dev](https://github.com/SchemaPlus/schema_dev) README.

  The matrix of configurations is specified in `schema_dev.yml` in
  the project root.


<!-- SCHEMA_DEV: TEMPLATE USES SCHEMA_DEV - end -->

<!-- SCHEMA_DEV: TEMPLATE USES SCHEMA_PLUS_CORE - begin -->
<!-- These lines are auto-inserted from a schema_dev template -->
* **schema_plus_core**: SchemaPlus::Views uses the SchemaPlus::Core API that
  provides middleware callback stacks to make it easy to extend
  ActiveRecord's behavior.  If that API is missing something you need for
  your contribution, please head over to
  [schema_plus_core](https://github/SchemaPlus/schema_plus_core) and open
  an issue or pull request.  

<!-- SCHEMA_DEV: TEMPLATE USES SCHEMA_PLUS_CORE - end -->

<!-- SCHEMA_DEV: TEMPLATE USES SCHEMA_MONKEY - begin -->
<!-- These lines are auto-inserted from a schema_dev template -->
* **schema_monkey**: SchemaPlus::Views is implemented as a
  [schema_monkey](https://github.com/SchemaPlus/schema_monkey) client,
  using [schema_monkey](https://github.com/SchemaPlus/schema_monkey)'s
  convention-based protocols for extending ActiveRecord and using middleware stacks.
  For more information see [schema_monkey](https://github.com/SchemaPlus/schema_monkey)'s README.

<!-- SCHEMA_DEV: TEMPLATE USES SCHEMA_MONKEY - end -->
