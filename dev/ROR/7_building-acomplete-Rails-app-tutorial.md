Building a complete Rails app tutorial
======================================

## Initializing

```shell
rails new maleta_de_cuentos
cd maleta_de_cuentos
git init
git add .
git commit -m "Initial commit"
bundle install
```

## Modelos de Tipo y Subtipo

```shell
rails g model Tipo nombre
```
El modelo Subtipo es una combinación de primary key, nombre y foreign key (FK) que apunta hacia un Tipo

```shell
rails g model Subtipo nombre tipo:references
```
models/tipo.rb:
```ruby
class Tipo < ApplicationRecord
  has_many :cuentos
end
```

models/subtipo.rb:
```ruby
class Subtipo < ApplicationRecord
  belongs_to :tipo
  has_many :cuentos
end
```
## Modelo de Cuento

```shell
rails g model Cuento titulo autor nacionalidad region editorial isbn personaje:text tema:text version:text archivo tipo:references subtipo:references
rake db:migrate
```

Crear el controlador con las acciones básicas:
```shell
rails g controller Cuentos index new create edit update show destroy
```

config/routes.rb:
```ruby
resources :cuentos
root to: 'cuentos#index'
```

app/controllers/cuentos_controller.rb:
```ruby
class CuentosController < ApplicationController

  before_action :set_cuento, only: [:show, :edit, :update]

  def index
    @cuentos = Cuento.order('titulo ASC')
  end

  def new
    @cuento = Cuento.new
    init_tipos_subtipos
  end

  def create
    @cuento = Cuento.new(cuento_params)

    if @cuento.save
      redirect_to cuentos_path
    else
      init_tipos_subtipos
      render :new
    end
  end

  def edit
  end

 def update
    if @cuento.update_attributes(cuento_params)
      redirect_to cuento_path(@cuento)
    else
      render :edit
    end
  end

  def show
  end

  def destroy
  end

  def cuento_params
    params.require(:cuento).permit(:titulo, :autor, :nacionalidad, :region, :tipo_id, :subtipo_id, :editorial, :isbn, :personaje, :tema, :version, :archivo)
  end

  def set_cuento
    @cuento = Cuento.find(params[:id])
  end

  def init_tipos_subtipos
    tipos = Tipo.all
    subtipos = Subtipo.where("tipo_id = ?", Tipo.first.id)
    @tipos_options = tipos.collect { |tipo|[tipo.nombre, tipo.id] }
    @subtipos_options = subtipos.collect { |subtipo|[subtipo.nombre, subtipo.id] }
  end

end


```


Construir la vista index:
views/cuentos/index.html.erb
```html
<h1>Cuentos</h1>
<%= link_to 'Subir nuevo cuento', new_cuento_path %>
<%= render @cuentos %>
```

Y el parcial correspondiente:
views/cuentos/_cuento.html.erb
```html
<h2><%= link_to cuento.titulo, cuento_path(cuento) %></h2>

<p><%= cuento.autor %></p>

<p><%= link_to 'Editar', edit_cuento_path(cuento) %></p>
<hr>
```

Fill the database by creating objects in db/seeds.rb and then rake db:seed


## Integrando Carrierwave
Poner la gem de Carrierwave en el Gemfile:

/Gemfile

```ruby
gem 'carrierwave', '~> 1.0'
```

Ejecuta en el terminal:
```shell
bundle install
```

Carrierwave almacena su configuración dentro de uploaders que están incluidos en tus modelos. Para generar un uploader, usa el siguiente comando:
```shell
rails generate uploader Archivo
```
Ahora, dentro de app/uploaders, encontrarás un nuevo archivo llamado archivo_uploader.rb. Nota que tiene algunos comentarios útiles y ejemplos, así que podrías usarlos para comenzar.

El uploader ya tiene ajustes por defecto, pero al menos necesitamos elegir en dónde serán almacenados los archivos subidos. Por ahora, empleemos almacenamiento de archivos:

uploaders/archivo_uploader.rb
```ruby
storage :file
```

y habilitación solo de pdf
uploaders/archivo_uploader.rb
```ruby
 def extension_whitelist
      #%w(jpg jpeg gif png)
      %w(pdf)
   end
```

Después, necesitamos incluir o montar este uploader al modelo:

models/cuento.rb
```ruby
class Cuento < ActiveRecord::Base
  mount_uploader :archivo, ArchivoUploader
end
```

Por defecto, los archivos serán colocados dentro del directorio public/uploads, así que es mejor exluirlo del sistema de control de versión:

.gitignore

```ruby
# Ignore the directory Carrierwave image uploads
/public/uploads
```
Podrás también modificar el método store_dir dentro de tu uploader para elegir alguna otra ubicación.


En este punto, vamos a crear una nueva vista y un formulario parcial para comenzar a subir archivos:

views/cuentos/new.html.erb
```ruby
<h1>Subir cuento</h1>

<%= render 'form', cuento: @cuento %>
```

views/cuentos/_form.html.erb
```ruby


<%= render 'shared/errors', object: cuento %>

<%= form_for cuento do |f| %>

  <fieldset>
    <legend>Datos generales:</legend>

      <div>
        <%= f.label :título %>
        <%= f.text_field :titulo, placeholder: "obligatorio" %>
      </div>

      <div>
        <%= f.label :autor %>
        <%= f.text_field :autor, placeholder: "obligatorio" %>
      </div>

      <div>
        <%= f.label :nacionalidad %>
        <%= f.text_field :nacionalidad, placeholder: "obligatorio" %>
      </div>

      <div>
        <%= f.label :región %>
        <%= f.text_field :region %>
      </div>

      <div>
        <%= f.label :tipo %>
        <%= f.select :tipo_id, options_for_select(@tipos_options), {:prompt => "--Selecciona tipo--"}, { id: 'tipos_select' } %>
        <%= f.select :subtipo_id, options_for_select(@subtipos_options), {:prompt => "--Selecciona subtipo--"}, { id: 'subtipos_select' } %>

      </div>

      <div>
        <%= f.label :editorial %>
        <%= f.text_field :editorial %>
      </div>

      <div>
        <%= f.label :isbn %>
        <%= f.text_field :isbn %>
      </div>

      <div>
        <%= f.label :versión %>
        <%= f.text_area :version, rows: 5, cols: 50, placeholder: "¿De dónde sale este cuento (abuela, campamento, etc.)?" %>
      </div>

  </fieldset>

  <fieldset>
    <legend>Contenido:</legend>

      <div>
        <%= f.label :personajes %>
        <%= f.text_area :personaje, rows: 5, cols: 50, placeholder: "Escribe los personajes principales del cuento separados por una coma." %>
      </div>

      <div>
        <%= f.label :tema %>
        <%= f.text_area :tema, rows: 5, cols: 50, placeholder: "¿Para qué usarías este cuento? Escribe el o los temas separados por una coma" %>
      </div>

   </fieldset>

  <div>
    <%= f.label :archivo %>
    <%= f.file_field :archivo %>
  </div>


  <%= f.submit %>
<% end %>



```

¡Eso es todo! Debes iniciar el servidor e intentar crear una publicación con una imagen.



## Visualizar Imágenes
Así qué, la única vista que no hemos creado aún es show. Agrégala ahora:

views/cuentos/show.html.erb
```ruby


<%= link_to 'Todos los cuentos', cuentos_path %>
<h1><%= @cuento.titulo %></h1>

<p>
  <strong>Autor:</strong>
  <%= @cuento.autor %>
</p>

<p>
  <strong>Nacionalidad:</strong>
  <%= @cuento.nacionalidad %>
</p>

<p>
  <strong>Región:</strong>
  <%= @cuento.region %>
</p>

<p>
  <strong>Tipo:</strong>
  <%= @cuento.tipo.nombre if !@cuento.tipo.nil?%>
</p>

<p>
  <strong>Subtipo:</strong>
  <%= @cuento.subtipo.nombre if !@cuento.subtipo.nil? %>
</p>

<p>
  <strong>Editorial:</strong>
  <%= @cuento.editorial %>
</p>

<p>
  <strong>ISBN:</strong>
  <%= @cuento.isbn %>
</p>

<p>
  <strong>Personaje(s):</strong>
  <%= @cuento.personaje %>
</p>

<p>
  <strong>Tema(s):</strong>
  <%= @cuento.tema %>
</p>

<p>
  <strong>Versión:</strong>
  <%= @cuento.version %>
</p>

<object data="<%=@cuento.archivo.url if @cuento.archivo? %>?#zoom=85&scrollbar=0&toolbar=0&navpanes=0" type="application/pdf" width="750" height="750">
    <embed src="<%=@cuento.archivo.url if @cuento.archivo?%>" type="application/pdf">
    <p>Este navegador no gestiona archivos pdf.</p>
</object>


<p><%= link_to 'Editar', edit_cuento_path(@cuento) %></p>






```

## Gestión de tipo de cuento

cuentos_controller.rb
```ruby
 def new
    @cuento = Cuento.new
    @tipos = Tipo.all
    @subtipos = Tipo.where("tipo_id = ?", Tipo.first.id)
  end
```

Add the following lines into app/views/welcome/index.html.erb. Point out how we set HTML id to the select boxes here. The cities_select id is important: we will update _this_ HTML component with AJAX.

1
2
3
4
5
6
7
8
9
<!-- app/views/cuentos/_form.html.erb -->

```html
<%= form_for :trip, url: {action: "show"}, html: {method: "get"} do |f| %>
  <%= f.select :tipo_id, options_for_select(@tipos.collect { |tipo|
    [tipo.nombre.titleize, tipo.id] }, 1), {}, { id: 'tipos_select' } %>
  <%= f.select :subtipo_id, options_for_select(@subtipos.collect { |subtipo|
    [subtipo.name.titleize, subtipo.id] }, 0), {}, { id: 'subtipos_select' } %>
  <%= f.submit "Go!" %>
<% end %>

```

source: https://kernelgarden.wordpress.com/2014/02/26/dynamic-select-boxes-in-rails-4/










# Scaffolding method

# Initializing
```shell
rails new maleta_de_cuentos
cd maleta_de_cuentos
git init
git add .
git commit -m "Initial commit"
bundle install
```

## Creating the models
rails g scaffold Usuario nombre_de_usuario nombre apellidos dni email contrasena
rake db:migrate

rails g scaffold Cuento titulo autor nacionalidad region editorial ISBN archivo

rake db:migrate


```shell
gem install carrierwave
gem install bootstrap-sass
```

Open up your gemfile and add the following two gems at the bottom:

```ruby
# gems for file upload management
gem 'carrierwave', '~> 0.9'
gem 'boostrap-sass', '~> 2.3.2'
```

```shell
rails g migration add_archivo_to_cuentos archivo
rails g uploader archivo
```

Now open the resume model and call the uploader as shown below. Add the following code into app/models/cuento.rb:
```ruby
class Cuento < ActiveRecord::Base
  mount_uploader :archivo, ArchivoUploader # Tells rails to use this uploader for this model.
   validates :titulo, presence: true # Make sure the cuento's title is present.
end
```


Before working on controller, we need to modify our config/routes.db as shown below: Add

root 'cuentos#index'


Lets us edit the controller as shown below.

class CuentosController < ApplicationController
   def index
      xxxxxx



Let's add bootstrap implementation in css file.css file could be in app/assets/stylesheets/resumes.css.scss

```ruby
@import "bootstrap";
```

Now open up app/views/layouts/application.html.erb and add code into the body tag as shown below −

```html
   <body>
      <div class = "container" style = "padding-top:20px;">
         <%= yield %>
      </div>
   </body>

```










