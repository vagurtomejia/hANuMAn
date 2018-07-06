# Rails

## Useful links

[Rails starter guides](http://edgeguides.rubyonrails.org/getting_started.html)
[Faker gem](https://github.com/stympy/faker)
[Rails cheatsheet](http://www.cheat-sheets.org/saved-copy/RubyOnRails-Cheatsheet-BlaineKendall.pdf)

## Useful commands
**list rails options:**
rails

**creates the rake and file structures (we use -d for soecifying the type of the database - sqlite by default )**
rails new -d postgresql hackernews

**generate models (unlike Sinatra, the generate models command creates the tables also)**
rails g model User (unlike Sinatra this creates the model also)

**list alll the application routes:**
rails routes

**generate migrations (like rake generate:migration on Sinatra)**
rails generate migration create_posts

**set the models and migrations**
like we used to do in sinatra in /models and /db/migrate

**create the database**
rake db:create

**run migrations**
rake db:migrate

**run the console for verifying if the models were created:**
rails console

**run rails server locally (equivalent of be shotgun in sinatra)**
rails s

**define the method in the controller class**



**define the routes in the config/routes.rb file**
root 'posts#index'
get 'posts' => 'posts#index'

**create the views:**
views/posts/index.html.erb


rake routes


get 'post'




**useful stuff**
er [TAB]


## Active Record migration Datatypes

:string - is for small data types such as a title.
:text - is for longer pieces of textual data, such as a paragraph of information
:binary - is for storing data such as images, audio, or movies.
:boolean - is for storing true or false values.
:date - store only the date
:datetime - store the date and time into a column.
:time - is for time only
:timestamp - for storing date and time into a column.(What's the difference between datetime and timestamp?)
:decimal - is for decimals (example of how to use decimals).
:float - is for decimals. (What's the difference between decimal and float?)
:integer - is for whole numbers.
:primary_key - unique key that can uniquely identify each row in a table

Note: You can access this list everytime you want:

```shell
rails generate model -h
```

## Example

rails g model User name password

rails g model Entry title content:text author:references

rails g controller Entries index, show, new, edit, create, update and destroy

/config/routes.rb
```ruby
resources :entries
root 'entries#index'
```


