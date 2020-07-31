# Rails

### Installs

[Rails Guides](https://guides.rubyonrails.org/v5.0/getting_started.html)
Requires:
- Ruby
- RubyGems [Guides](https://guides.rubygems.org/)
- Might require other installations on Windows

### Rails:

Rails is a web application development framework written in the Ruby programming language. Rails is a model–view–controller (MVC) framework, providing default structures for a database and how to navigate them based on client app needs.

There are some parallels:
![Express vs. Rails](https://i.imgur.com/zIiJf0L.png)

### Getting started with Rails
##### Long version:

```sql
rails new test_new
# might have to install yarn, and run install:webpack
rm -rf test_new

# note that rails spins up a server with a git repo and defaults to a different db

tflosse@Tamaras-MBP student_examples % rails new intro_app_api --api -d postgresql --skip-git
 
 tflosse@Tamaras-MBP intro_app_api % rails s
# will run the server in localhost:3000 or attributed url
=> Booting Puma
=> Rails 6.0.3.2 application starting in development 
=> Run `rails server --help` for more startup options
Puma starting in single mode...
* Version 4.3.5 (ruby 2.6.3-p62), codename: Mysterious Traveller
* Min threads: 5, max threads: 5
* Environment: development
* Listening on tcp://127.0.0.1:3000
* Listening on tcp://[::1]:3000
Use Ctrl-C to stop

# might gat the following if the db does not exist:
ActiveRecord::NoDatabaseError (FATAL:  database "intro_app_api_development" does not exist
):

tflosse@Tamaras-MBP intro_app_api % rails db:create
Created database 'intro_app_api_development'
Created database 'intro_app_api_test'
tflosse@Tamaras-MBP intro_app_api % 

tflosse@Tamaras-MBP intro_app_api % rails dbconsole
psql (12.3)
Type "help" for help.

intro_app_api_development=# 
# You can then run sql commands in your DB

tflosse@Tamaras-MBP intro_app_api % cd db 
tflosse@Tamaras-MBP db % rails generate migration CreateTodos
Running via Spring preloader in process 17804
      invoke  active_record
      create    db/migrate/20200731172000_create_todos.rb
# Creates a migrate folder and file
```
![db folder](https://i.imgur.com/fsxr2Ex.png)

```ruby
class CreateTodos < ActiveRecord::Migration[6.0]
  def change
    create_table :todos do |t|
    end
  end
end
```
**Add details for `change` method** in migration file:
```ruby
class CreateTodos < ActiveRecord::Migration[6.0]
  def change
    create_table :todos do |t|
      t.string :title
      t.boolean :completed
    end
  end
end
```

#### ! Important: after the migration is ran and schema file created, those files should not be changed.

```sql
tflosse@Tamaras-MBP db % rails db:migrate
# runs all migrations and creates the tables, as well as a schema.rb file
== 20200731172000 CreateTodos: migrating ======================================
-- create_table(:todos)
   -> 0.0619s
== 20200731172000 CreateTodos: migrated (0.0620s) =============================

tflosse@Tamaras-MBP db % rails dbconsole
psql (12.3)
Type "help" for help.

intro_app_api_development=# SELECT * FROM todos;
 id | title | completed 
----+-------+-----------
(0 rows)

intro_app_api_development=# \q
# to exit dbconsole
```

##### Short version:

```sql
rails new blog_app_api --api -d postgresql --skip-git
cd blog_app_api
cd db 
rails db:create
-- Created database 'blog_app_api_development'
-- Created database 'blog_app_api_test'
rails g migration CreatePosts
-- Running via Spring preloader in process 18643
--       invoke  active_record
--       create    db/migrate/20200731183903_create_posts.rb

# At this point, add to migration file to create the needed columns in your table. 
-- in .rb file:
class CreatePosts < ActiveRecord::Migration[6.0]
  def change
    create_table :posts do |t|
    -- columns go here
      t.string :title 
      t.string :content
      t.string :author
    end
  end
end
-- back to terminal

rails db:migrate
-- == 20200731183903 CreatePosts: migrating ======================================
-- -- create_table(:posts)
--    -> 0.0630s
-- == 20200731183903 CreatePosts: migrated (0.0632s) =============================

rails dbconsole
-- psql (12.3)
-- Type "help" for help.

blog_app_api_development=# SELECT * FROM posts;
 id | title | content | author 
----+-------+---------+--------
(0 rows)

blog_app_api_development=# \q
```
****

### Creating and managing models

```sql
cd app 
cd models 
touch todo.rb
```
![folders](https://i.imgur.com/sA4QzBK.png)
**Note** Model files are created ***Capilatized and Singular***.
In `todo.rb`:
```ruby
class Todo < ApplicationRecord
end
```

You can use the `rails console` commands
- "string".singularize
- "string".pluralize
to check on singular/plural as Rails will read and references them. **Naming conventions will matter here**

### ActiveRecord

[Ruby ActiveRecord Basics](https://guides.rubyonrails.org/active_record_basics.html)

Active Record is a **ORM** framework, short for **Object Relational Mapping**. Much like using Mongoose with Mongo and Express, Active Record wraps around SQL and Rails, and allows us the following:
- represent models and their data
- represent associations between these models
- represent inheritance hierarchies through related models
- validate models before they get persisted to the database
- perform database operations in an object-oriented fashion
- Active Record is the M in MVC - the model - which is the layer of the system responsible for representing business data and logic.


> rails console or rails c

allows us to communicate with DB more simply.
```sql
irb(main):001:0> Todo.count
--    (3.7ms)  SELECT COUNT(*) FROM "todos"
-- => 0
-- irb(main):002:0> 
irb(main):004:0> todo = Todo.new(title: "First example", completed: true)
-- => #<Todo id: nil, title: "First example", completed: true>
irb(main):005:0> todo.save
--    (3.3ms)  BEGIN
--   Todo Create (41.6ms)  INSERT INTO "todos" ("title", "completed") VALUES ($1, $2) RETURNING "id"  [["title", "First example"], ["completed", true]]
--    (3.5ms)  COMMIT
-- => true
-- irb(main):006:0> 
irb(main):006:0> Todo.create(title: "Second example", completed:false)
--    (0.2ms)  BEGIN
--   Todo Create (0.4ms)  INSERT INTO "todos" ("title", "completed") VALUES ($1, $2) RETURNING "id"  [["title", "Second example"], ["completed", false]]
--    (6.9ms)  COMMIT
-- => #<Todo id: 2, title: "Second example", completed: false>
irb(main):007:0> Todo.all
--   Todo Load (0.7ms)  SELECT "todos".* FROM "todos" LIMIT $1  [["LIMIT", 11]]
-- => #<ActiveRecord::Relation [#<Todo id: 1, title: "First example", completed: true>, #<Todo id: 2, title: "Second example", completed: false>]>
-- irb(main):008:0> 
```

In the `rails dbconsole`, you can see:
```sql
intro_app_api_development=# SELECT * FROM todos;
 id |     title      | completed 
----+----------------+-----------
  1 | First example  | t
  2 | Second example | f
(2 rows)
```

This might not read as easy to read...
`hirb` will make it more manageable and easier on the eye:
Add `gem 'hirb'` in Gemfile
then run `bundle install` to install `hirb`
***You would have to 're-bundle' every time a gem is installed.***

Back tot he rails console `rails c`
```sql
irb(main):001:0> require 'hirb'
-- => false
irb(main):002:0> Hirb.enable
-- => true
irb(main):004:0> Todo.all
--   Todo Load (0.6ms)  SELECT "todos".* FROM "todos"
-- +----+----------------+-----------+
-- | id | title          | completed |
-- +----+----------------+-----------+
-- | 1  | First example  | true      |
-- | 2  | Second example | false     |
-- +----+----------------+-----------+
-- 2 rows in set
-- irb(main):005:0> 
```

Ruby Docs for Querying here: [ActiveRecord Querying](https://guides.rubyonrails.org/active_record_querying.html)

Some other examples in the rails console:
![crud in rails with hirb](https://i.imgur.com/nJzQMyh.png)

****

### Migrations for modifying Schemas

#### Adding a column:
```
rails g migration AddDescriptionToTodos

<!-- To delete a migration -->
rails destroy migration <name_of_migration>
```
In the `.rb file` created for the migration:
```rb
class AddDescriptionToTodos < ActiveRecord::Migration
  def change
    # Method    Table   Column       Datatype
    add_column :todos, :description, :string
  end
end
```
Then run `rails db:migrate`

Double-check in the `rails dbconsole` with a SQL command (SELECT * FROM todos) or is `rails console` with ActiveRecord command (Todo.all) or **`Todo.column_names`
```
irb(main):003:0> Todo.column_names
=> ["id", "title", "completed", "description"]
```

You can also check the `schema.rb` file:
```rb
ActiveRecord::Schema.define(version: 2020_07_31_200728) do

  # These are extensions that must be enabled in order to support this database
  enable_extension "plpgsql"

  create_table "todos", force: :cascade do |t|
    t.string "title"
    t.boolean "completed"
    t.string "description"
  end

end
```

#### Changing a column:
```
rails g migration ChangeDescriptionInTodosToDetails
```
In the `.rb file` created for the migration:
```rb
class ChangeDescriptionInTodosToDetails < ActiveRecord::Migration[6.0]
  def change
    # Method      Table     Column       New name
    rename_column :todos, :description, :description
  end
end
```
Then run `rails db:migrate`

You can also check the `schema.rb` file:
```rb
ActiveRecord::Schema.define(version: 2020_07_31_200728) do

  # These are extensions that must be enabled in order to support this database
  enable_extension "plpgsql"

  create_table "todos", force: :cascade do |t|
    t.string "title"
    t.boolean "completed"
    t.string "details"
  end

end
```

#### Removing a column:
```
rails g migration RemoveDetailsInTodos
```
In the `.rb file` created for the migration:
```rb
class ChangeDescriptionInTodosToDetails < ActiveRecord::Migration[6.0]
  def change
    # Method      Table     Column    Type
    remove_column :todos, :details, :string
  end
end
```
Then run `rails db:migrate`

****

### Lastly, going back on mistakes:
```sql
rails db:rollback
-- Rolls back the last migration.

rails db:migrate VERSION=n
-- To go to back to n migrates to that version number, where the version numbers come from listing the migrations sequentially.

rails db:reset
-- To re-do your migrations from the beginning, AND seed the database

rails destroy migration NameOfMigration
-- To delete a migration

-- Note: It is advisable only to delete the final migration, and that that migration has not already been run. This is because successive migrations usually depend on one another.

rails d migration NameOfMigration
-- Shorthand, destroy can be written as d
```

****

###### Below is a "easy-to-navigate" console response with rails options:
```
Usage:
  rails new APP_PATH [options]

Options:
      [--skip-namespace], [--no-skip-namespace]              # Skip namespace (affects only isolated applications)
  -r, [--ruby=PATH]                                          # Path to the Ruby binary of your choice
                                                             # Default: /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/bin/ruby
  -m, [--template=TEMPLATE]                                  # Path to some application template (can be a filesystem path or URL)
  -d, [--database=DATABASE]                                  # Preconfigure for selected database (options: mysql/postgresql/sqlite3/oracle/frontbase/ibm_db/sqlserver/jdbcmysql/jdbcsqlite3/jdbcpostgresql/jdbc)
                                                             # Default: sqlite3
      [--skip-gemfile], [--no-skip-gemfile]                  # Don't create a Gemfile
  -G, [--skip-git], [--no-skip-git]                          # Skip .gitignore file
      [--skip-keeps], [--no-skip-keeps]                      # Skip source control .keep files
  -M, [--skip-action-mailer], [--no-skip-action-mailer]      # Skip Action Mailer files
      [--skip-action-mailbox], [--no-skip-action-mailbox]    # Skip Action Mailbox gem
      [--skip-action-text], [--no-skip-action-text]          # Skip Action Text gem
  -O, [--skip-active-record], [--no-skip-active-record]      # Skip Active Record files
      [--skip-active-storage], [--no-skip-active-storage]    # Skip Active Storage files
  -P, [--skip-puma], [--no-skip-puma]                        # Skip Puma related files
  -C, [--skip-action-cable], [--no-skip-action-cable]        # Skip Action Cable files
  -S, [--skip-sprockets], [--no-skip-sprockets]              # Skip Sprockets files
      [--skip-spring], [--no-skip-spring]                    # Don't install Spring application preloader
      [--skip-listen], [--no-skip-listen]                    # Don't generate configuration that depends on the listen gem
  -J, [--skip-javascript], [--no-skip-javascript]            # Skip JavaScript files
      [--skip-turbolinks], [--no-skip-turbolinks]            # Skip turbolinks gem
  -T, [--skip-test], [--no-skip-test]                        # Skip test files
      [--skip-system-test], [--no-skip-system-test]          # Skip system test files
      [--skip-bootsnap], [--no-skip-bootsnap]                # Skip bootsnap gem
      [--dev], [--no-dev]                                    # Setup the application with Gemfile pointing to your Rails checkout
      [--edge], [--no-edge]                                  # Setup the application with Gemfile pointing to Rails repository
      [--rc=RC]                                              # Path to file containing extra configuration options for rails command
      [--no-rc], [--no-no-rc]                                # Skip loading of extra configuration options from .railsrc file
      [--api], [--no-api]                                    # Preconfigure smaller stack for API only apps
  -B, [--skip-bundle], [--no-skip-bundle]                    # Don't run bundle install
  --webpacker, [--webpack=WEBPACK]                           # Preconfigure Webpack with a particular framework (options: react, vue, angular, elm, stimulus)
      [--skip-webpack-install], [--no-skip-webpack-install]  # Don't run Webpack install

Runtime options:
  -f, [--force]                    # Overwrite files that already exist
  -p, [--pretend], [--no-pretend]  # Run but do not make any changes
  -q, [--quiet], [--no-quiet]      # Suppress status output
  -s, [--skip], [--no-skip]        # Skip files that already exist

Rails options:
  -h, [--help], [--no-help]        # Show this help message and quit
  -v, [--version], [--no-version]  # Show Rails version number and quit
  ```