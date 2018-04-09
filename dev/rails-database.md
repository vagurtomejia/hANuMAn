
# Rails database

## SQLite
Rails uses SQLite by default.
SQLite is self-contained, server-less, zero-configuration, transactional,relational SQLdatabase engine. It is the more deployedSQL database engine in the world.

File that says where the database is:
config/database.yml

Where the database is actually stored:
db/development.sqlite3

There is a built-in command-line DB viewer on Rails:
To launch the SQLite console:
```shell
rails db
```

.tables
.headers on
.mode columns
select * from cars;

Other example of tool that you can use to view the contents and schema of your database:sql browser


## Scaffolding
code generator for entities.
Scaffolding creates:
+ Migration
+ Model
+ Routes
+ Controller with actions
+ Views
+ Generates JSON response
+ And more ...

You can do it right after the creation of the project.

Usage:
rails generate scaffold NAME [field[:type][:index] field[:type][:index]] [options]

```shell
$rails g scaffold HighScore game:string score:integer
or
$rails g scaffold HighScore game score:integer
```
(if no type is specified for a fields, it is assumed to be a string)

$rails g scaffold Car make color year:integer

## Applying Scaffolding to DB == migrating the database
Rake task
```shell
$rake db:migrate
```