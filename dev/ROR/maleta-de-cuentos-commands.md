
rails new maleta_de_cuentos
cd maleta_de_cuentos
git init
git add .
git commit -m "Initial commit"
---
bundle install

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



====================
====================

```shell
rails new maleta_de_cuentos
cd maleta_de_cuentos
git init
git add .
git commit -m "Initial commit"
bundle install

rails g model Cuento titulo autor nacionalidad region tipo editorial isbn personaje:text tema:text version:text archivo
rake db:migrate
```

Crear el controlador con las acciones básicas:
```shell
rails g controller Cuentos index create new edit show update destroy
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

  def show
  end

  def new
    @cuento = Cuento.new
  end

  def create
    @cuento = Cuento.new(cuento_params)
    if @cuento.save
      redirect_to cuentos_path
    else
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

  private

  def cuento_params
    params.require(:cuento).permit(:titulo, :autor, :nacionalidad, :region, :tipo, :editorial, :isbn, :personaje, :tema, :version, :archivo)
  end

  def set_cuento
    @cuento = Cuento.find(params[:id])
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
<!-- <p><%= truncate(cuento.tema, length: 150) %></p> -->

<p><%= link_to 'Edit', edit_cuento_path(cuento) %></p>
<hr>
```


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
public/uploads
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

<%= form_for cuento do |f| %>
  <div>
    <%= f.label :título %>
    <%= f.text_field :titulo %>
  </div>

  <div>
    <%= f.label :autor %>
    <%= f.text_field :autor %>
  </div>

  <div>
    <%= f.label :nacionalidad %>
    <%= f.text_field :nacionalidad %>
  </div>

  <div>
    <%= f.label :región %>
    <%= f.text_field :region %>
  </div>

  <div>
    <%= f.label :tipo %>
    <%= f.text_field :tipo %>
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
    <%= f.label :personaje %>
    <%= f.text_area :personaje %>
  </div>

  <div>
    <%= f.label :tema %>
    <%= f.text_area :tema %>
  </div>

  <div>
    <%= f.label :version %>
    <%= f.text_area :version %>
  </div>

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
  <%= @cuento.region %>
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
  <strong>Version:</strong>
  <%= @cuento.version %>
</p>

<object data="<%=@cuento.archivo.url if @cuento.archivo? %>?#zoom=85&scrollbar=0&toolbar=0&navpanes=0" type="application/pdf" width="750" height="750">
    <embed src="<%=@cuento.archivo.url if @cuento.archivo?%>" type="application/pdf">
    <p>Este navegador no gestiona archivos pdf.</p>
</object>


<p><%= link_to 'Edit', edit_cuento_path(@cuento) %></p>
```

## Gestión de tipo de cuento

rails g model Tipo nombre

El modelo Subtipo es una combinación de primary key, nombre y foreign key (FK) que apunta hacia un Tipo

rails g model Subtipo nombre tipo:references

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












