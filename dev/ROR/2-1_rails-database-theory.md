
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

Some commands that can be used:
.tables
.schema [NAME-OF-THE-TABLE]
.headers on
.mode columns
select * from [NAME-OF-THE-TABLE];
.quit

Other example of tool that you can use to view the contents and schema of your database:sql browser

To silence tge sql in the rails console:
```shell
rails c
ActiveRecord::Base.logger = nil
```


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

## Migrations
Migrations are useful to allow the database to be Agile and not to be locked in a particular dialect.
A migration is a file that:
- starts with a timestamp
- is followed by some name, which becomes the name of the class
- the timestamp sort of acts like a database version and defines the order in which you need to apply the modifications in your database

### Migrations creation
Migrations can be created in 3 ways:
- by hand
- scaffold generator creates a migration
- using the explicit migrations generator

### Applying migrations
Once the migration is created, the database needs to be applied (updated).
2 migrations can not have the same class name

Run the Rake task to apply all the migrationsin db/migrate folder in (timestamp) order.
```shell
$rake db:migrate
```

### Rollback
To undo the last migration (rollback to the previous migration)
```shell
$rake db:rollback
```

## Creating tables
+ table names are always plural in Rails, by convention
+ and `id` column is automatically created to be used as a primary key
+ timestamps methods is automatically added to the migrations
+ `create_table` and `drop_table` creates and drops the table

###Adding/removing columns
add_column :table_name, :column_name, :column_type
remove_column :table_name, :column_name

### Some column options:
null: true or false
limit: size
default: value
precision: value
scale: value

### Migrations/ s documentation
http://edgeguides.rubyonrails.org/active_record_migrations.html



## Active Record
ActiveRecord is the name of Rails' default ORM

### ORM model
Object-Relational Mapping
It maps from an object to relational database.

Bridges the gap betweenrelational databases which aredesignedaround mathematical set theory and OOP languagesthat deal with objects and their behavior.

In Rails, the Model usually uses some ORM framework.

### AR Design patterns
Design Pattern = Convention that people follows over and over and then it is written up as something that people should follow. It's the best practise.

The Design Pattern of AR is basically: you have a Model/class, ie. Person, and you store:
+ the attributes of that class, ie. first_name, last_name
+ AND the logic of how you are going to store that person

### AR Conventions

Three prerequises:

+ AR needs to know how to find your database (info read from config/database.yml file)
+ Expects the table to have a primary key named `id`
+ Class name is singular
+ DB table name is plural:
  + cars table for Car objects
  + people for Person
  + the inflections are located into config/initializers/inflections.rb file and you can add your own

### AR CRUD
+ Create
+ Retrieve
+ Update
+ Delete

#### Create
3 ways to create a record in the database:
+ empty constructor and pass attributes to set the values and then call `save`:
  ```ruby
  p1 = Person.new
  p1.first_name = "vero"
  p1.last_name = "AM"
  p1.save
  ```
+ pass a hash of attributes into the constructor and then call `save`
  ```ruby
  p2 = Person.new(first_name: "vero", last_name: "AM")
  p2.save
  ```
+ create a record directly into the database (just one step) by calling the `create` method with a hash:
  ```ruby
  p2 = Person.create(first_name: "vero", last_name: "AM")
  ```

#### Retrieve
+ find(id) or find(id1,id2)
  throws a RecordNotFound exception if the record is not found into the database
+ first, last, all
  return the resultor nil if nothing is found
  ```ruby
  Person.first
  ```
+ order(:column) or order(column: :desc)
  allows ordering ascending or descending
  ```ruby
  Person.all.order(first_name: :desc)
  ```
+ take, pluck
  allows to narrow down which fields are coming back from the database
  need to call at the end!
  ie: if I want to take 2 people from the database:
  ```ruby
  Person.take 2
  ```
  Pull in only the first names of people in the datbase:
  ```ruby
  Person.pluck(:first_name)
  ```
+ where(hash)
  Returns ActiveRecord::Relation(same as `all)
  ie:
  ```ruby
  Person.where(last_name: "AM")
  ```
+ find_by(conditions_hash)
  Returns a single result or `nil` if a record with the specified conditions is not found
  ie:
  ```ruby
  Person.find_by(last_name: "AM")
  ```
+ find_by!(conditions_hash)
  Same as find_by but throws an exception if a record with the specified conditions is not found
  ie:
  ```ruby
  Person.find_by!(last_name: "AM")
  ```
+ limit(n)
  limits how many records come back
+ offset(n)
  don't start from the beginning, skip a few records
+ We can combine limit and offset to page througt a large collection of records
  ie:
  Person.count
  Person.offset(1).limit(1)


#### Update
2 ways to do it:
+ retrieve a record, modify the values and then call `save`
```ruby
vero = Person.find_by first_name: "vero"
vero.last_name = "Hada"
vero.save
```

+ retrieve a record, and then call `update`passing in ahash of attributeswith new values
```ruby
Person.find_by(first_name: "vero").update(first_name: "veronica")
```
+ There is also `update_all` for batch updates.
Can be chained to the end of `where`.

#### Delete
+ destroy(id) or destroy
  removes a particular instance from the dB
  instantiates an object firstand performs callbacksbefore removing
  ```ruby
  vero = Person.find_by first_name: "vero"
  vero.destroy
  ```

+ delete(id)
  removes the row from DB
  ```ruby
  vero = Person.find_by first_name: "vero"
  Person.delete(vero.id)
  ```

+ delete_all
  Warning! Deletes all the records!

### Seeds
To populate the development data, Rails provides db/seeds.rb.
Just fill the file with your seeds (always use 'create!'') and run:
```ruby
  rake db:seed
```

### Advanced querying

We can easily include SQL fragments in queries. ie:
Person.where("age BETWEEN 30 and 33").to_a
Person.find_by("first_name LIKE '%man'")



#### Beware of SQL injection!
What is SQL injection?
Manipulate raw SQL to hack into a database: malicious dropping, deleting tables orgaining access to confidential information.
ALways useeither the Arrayor Hash condition Syntax to avoid SQL injection!

#### Avoiding SQL injection
1. Array condition syntax
SpecifySQL fragment  with ? followed by values (parameters)

```ruby
Person.where("age BETWEEN ? AND ?", 28, 34).to_a
Person.where("first_name LIKE ? OR last_name LIKE ?", '%J%', '%J%').to_a
```

2. Hash condition syntax
Instead of "?"you specify symbolswhich map to the valuesin the hash passedin as a second parameter.

```ruby
Person.where("age BETWEEN :min_age AND :max_age", min_age: 28, max_age:34).to_a
Person.where("first_name LIKE :pattern OR last_name LIKE :pattern", pattern: '%J%').to_a
```
#### N + 1 Queries issue
For displaying the wight for all the people we have to get this information from the PersonalInfo table. The query will be:
Person.all.each { |p| puts p.personal_info.weight }
=> N + 1 queries

Instead we can also say that when we make a query to the Person table, it includes the PersonalInfo data ():
Person.includes(:personal_info).all.each { |p| puts p.personal_info.weight }
=> just 2 queries!

#### Transactions
Eveything we do in AR is a transaction


## Documentation
[ROR Active Record Basics documentation](http://guides.rubyonrails.org/active_record_basics.html)
[ROR Active Record Querying documentation](http://guides.rubyonrails.org/active_record_querying.html)



Creating a rails app with database commands suite:
----------------------------------------------------

```shell
rails new todolists
```

Add the following specification to your Gemfile to enable rspec testing:
```ruby
group :test do
gem 'rspec-rails', '~> 3.0'
end
```

```shell
cd todolists
git init
git add .
git commit -m "Initial commit"
...
bundle install
git add .
git commit -m "Add required gems to the gemfile"
```

From the todolists application root directory, initialize the rspec tests using:
```shell
rails generate rspec:install
```

Add the following line to .rspec to add verbose output to test results:
```ruby
--format documentation
```


```shell
git commit -m "Adds rspec tests"

rails g scaffold User username password_digest
rake db:migrate

rails g scaffold Profile gender birth_year:integer first_name last_name
rake db:migrate

rails g scaffold TodoList list_name list_due_date:date
rake db:migrate

rails g scaffold TodoItem due_date:date title description:text completed:boolean
rake db:migrate

```

