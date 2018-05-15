
+ Created the initial cars schema
$rails g scaffold Car make color year:integer

$rake db:migrate

+ Add a `price` column to the cars schema
$rails g migration add_price_to_cars 'price:decimal{10,2}'

$rake db:migrate

+ Rename the make column
$rails g migration rename_make_to_company
And then into the migration file generated, manually add the method (the code of a rename is not autmatically added by Rails):
def change
    rename_column :cars, :make,:company
end
