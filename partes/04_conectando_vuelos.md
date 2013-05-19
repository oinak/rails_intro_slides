!SLIDE centered
<div class="rails">&nbsp; </div>
# Introducción a Ruby on Rails #
# 4 - Conectando vuelos #
Mayo 2013
<br>Fernando Martínez
<br>
<div class="redradix">&nbsp; </div>

!SLIDE subsection
# Parte 4 #

!SLIDE bullets incremental
## MOAR MODELS! ##
* distintas entidades
* y sus relaciones
* y magia!
* en realidad es OO
* objetos como atributos de otros
* <tt>@post.comments</tt>

!SLIDE center
# O\_o #
![Russian dolls](russian-dolls.jpg)

!SLIDE smbullets incremental
# RELACIONES ##
* belongs\_to
* has\_many
* has\_one
* has\_many :through
* has\_one :through
* has\_and\_belongs\_to\_many

!SLIDE center
# WTF #
![Jackie WTF](habtm-migration.jpg)
dependiendo de la experiencia de cada uno en BBDD relacionales

!SLIDE bullets incremental
## por partes: como Jack el destripador ##
* veamos <tt>belongs\_to</tt> y <tt>has\_many</tt>

!SLIDE smbullets incremental
## belongs_to ##

    @@@ ruby
    class Edition < ActiveRecord::Base
      belongs_to :course, :dependent => :destroy
    end

* el modelo que pertenece depende del otro
* y almacena su id (hay que migrar)
* puede destruirse automaticamente (Samurai)

!SLIDE bullets incremental
## belongs\_to nos da ##
    @@@ ruby
    association(force_reload = false)
    association=(associate)
    build_association(attributes = {})
    create_association(attributes = {})

¿Como se llaman las de <tt>Edition</tt> ?

!SLIDE bullets incremental
## belongs\_to nos da ##
    @@@ ruby
    @edition.course(force_reload = false)
    @edition.course=(associate)
    @edition.build_course(attributes = {})
    @edition.create_course(attributes = {})

.notes cuidado con los nombres y los metodos de AR!

!SLIDE bullets incremental
# pero no me voy a acordar! #
* ni falta que hace
* se irá quedando
* hasta entonces...
* para recordar uno: <http://goo.gl/it6eZ>
* para elegir entre las que hay: <http://goo.gl/TCeI9>

!SLIDE bullets incremental
## ¿estaban ya en favoritos? ##
* <http://ruby-doc.com/>
* <http://api.rubyonrails.org/>
* <http://apidock.com/>
* propina: <http://dochub.io/>

!SLIDE bullets incremental
## belongs\_to: opciones ##
<tt>:autosave, :class\_name, :conditions, :counter\_cache, :dependent, :foreign\_key, :include, :inverse\_of,
:polymorphic, :readonly, :select, :touch, :validate</tt>

* para saltarse convención,
* lo mejor: no, hasta que os haga falta
* los defaults son astutos
* o funcionar de otra forma (veremos)

!SLIDE smbullets incremental
## belongs_to: opciones destacadas ##
* <tt>:dependent</tt> - ¿qué hacer en asociado al borrame?
* <tt>:inverse\_of</tt> - informa a <tt>has\_many</tt> (!duplicados)

.notes dependent => :destroy mejor en has_many

!SLIDE bullets incremental
## has\_one ##
* relacion 1:1
* muy parecido a <tt>belongs\_to</tt>
* mismos métodos 'gratis'
* diferencia sobre todo conceptual
* el *id* esta en el <tt>belongs\_to</tt>

.notes ¿quién pertenece a quién? ejemplos

!SLIDE code
# has\_many #
    @@@ ruby
    class Course < ActiveRecord::Base
      has_many :editions, :inverse_of => :course
    end

!SLIDE code
## has\_many ##
    @@@ ruby
    collection(force_reload = false)
    collection<<(object, …)
    collection.delete(object, …)
    collection=objects
    collection_singular_ids
    collection_singular_ids=ids
    collection.clear
    collection.empty?
    collection.size
    collection.find(…)
    collection.where(…)
    collection.exists?(…)
    collection.build(attributes = {}, …)
    collection.create(attributes = {})

!SLIDE smbullets incremental
## ¿qué vemos respecto a <tt>belongs_to</tt>? ##
* hay más métodos
* <tt>bulid</tt> y <tt>create</tt> son distintos
* <tt>&lt;&lt;, empty, size, delete</tt> funciona como <tt>Array</tt>

!SLIDE smbullets incremental
## antes de seguir... ##
* veámoslo en la aplicación
* un _Curso_ se puede hacer varias veces
* separemos la identidad del resto
* creemos <tt>Edition</tt>

!SLIDE bullets incremental
## Ejercicio ##
* Un curso tiene ediciones
* Una edición pertenece a un curso
* se queda las fechas, ubicación
* tiene <tt>seats</tt>(aforo), precio

!SLIDE smbullets incremental
## Ejercicio:notas ##
* vamos a mezclar cosas aun no vistas...
* ...para que el ejemplo se más real
* ¿Formularios?
* Veamos algo de vistas/rutas/controlador

!SLIDE
## rutas anidadas ##
<style>
  tbody tr:nth-child(even){ background-color: #fdd; }
   table tbody td{padding: .5em; vertical-align: middle}
</style>
<table style="width:90%;font-size:110%;">
 <tbody><tr>
 <th><span class="caps">HTTP</span> Verb </th> <th>Path </th> <th>action </th> <th>used for </th>
 </tr><tr>
 <td><span class="caps">GET</span> </td> <td>/courses/:course_id/editions </td>
 <td>index </td> <td>display a list of all editions for a specific course </td>
 </tr> <tr>
 <td><span class="caps">GET</span> </td> <td>/courses/:course_id/editions/new </td>
 <td>new </td> <td>return an <span class="caps">HTML</span> form for creating a new edition belonging to a specific course </td>
 </tr><tr>
 <td><span class="caps">POST</span> </td> <td>/courses/:course_id/editions </td>
 <td>create </td> <td>create a new edition belonging to a specific course </td>
 </tr><tr>
 <td><span class="caps">GET</span> </td> <td>/courses/:course_id/editions/:id </td>
 <td>show </td> <td>display a specific edition belonging to a specific course </td>
 </tr><tr>
 <td><span class="caps">GET</span> </td> <td>/courses/:course_id/editions/:id/edit </td>
 <td>edit </td> <td>return an <span class="caps">HTML</span> form for editing an edition belonging to a specific course </td>
 </tr><tr>
 <td><span class="caps">PUT</span> </td> <td>/courses/:course_id/editions/:id </td>
 <td>update </td> <td>update a specific edition belonging to a specific course </td>
 </tr><tr>
 <td><span class="caps">DELETE</span> </td> <td>/courses/:course_id/editions/:id </td>
 <td>destroy </td> <td>delete a specific edition belonging to a specific course </td>
 </tr></tbody></table>

!SLIDE smbullets incremental
# ¿Cómo lo hacemos? #
* todos juntos o por parejas
* partimos del _scaffold_
* sin formulario anidado (para simplificar)
* sin gemas de formularios (por ahora)
* mirando: <http://goo.gl/7MhPA> (DRY :-)
* <tt>Parent</tt> &#8594; <tt>Course</tt>, <tt>Child</tt> &#8594; <tt>Edition</tt>
* ¡Ojo con las migraciones! que ya tenemos <tt>Course</tt>

!SLIDE commandline incremental
## Ejercicio ayudas ##
    $ rails g scaffold Edition start:date finish:date seats:integer price_in_cents:integer location:string course:references
      invoke  active_record
      create    db/migrate/20130503213742_create_editions.rb
      create    app/models/edition.rb
      invoke    test_unit
    ...

!SLIDE smbullets incremental
## y después ##
* relaciones en los modelos
* rutas anidadas
* quitar <tt>start_date</tt> y <tt>location</tt> de <tt>Course</tt>

!SLIDE smbullets incremental
## visita rapida al mundo de los controladores ##
* <tt>load\_course</tt> en <tt>EditionsController</tt>
* variables de instancia en controlador
* helpers de ruta y redirecciones
* formularios: <tt>number\_to\_currency</tt>
* atributos proxy <tt>price, price=</tt>
* testeos y validaciones (*)

!SLIDE bullets incremental
## relaciones en los modelos
* como hemos visto en el tema
* las hacemos inversas

!SLIDE bullets incremental
## rutas anidadas
* como en el enlace
* las probamos con <tt>rake routes</tt>

!SLIDE bullets incremental
## quitar <tt>start_date</tt> y <tt>location</tt> de course
* editando la migración
* copiando los datos de <tt>Course</tt>
* borrando las columnas duplicadas
* con <tt>up</tt> y <tt>down</tt>
* conservando datos en ambos!

!SLIDE bullets incremental
## ya que estamos ##
* Evolución de datos
* planificación
* integridad referencial

!SLIDE bullets incremental
## <tt>load\_course</tt> en <tt>EditionsController</tt>
* un metodo privado en el controlador
* si es privado no es una _acción_
* ¿es DRY? hablemos de CanCan

!SLIDE bullets incremental
## variables de instancia en controlador
* cargar a partir de <tt>@course</tt>
* <tt>@course.editions.find(params[:id])</tt>
* seguridad estructural (Diáspora)

!SLIDE bullets incremental
## helpers de ruta en redirecciones
* más magia de nombres...
* que no tenemos que recordar
* <tt>rake routes</tt>

!SLIDE bullets incremental
## formularios: básico
* <tt>form_helper</tt>
* hay más en la Parte 6
* si os suena a uzbeko lo miramos

!SLIDE code
## formularios (REST)
    @@@ html
    <%= form_for(@course) do |f| %>
     <%= f.label :title %>: <%= f.text_field :title %>
     <%= f.label :description %>: <%= f.text_field :description %>
     <%= f.submit %>
    <% end %>

!SLIDE smbullets incremental
## que más tienen los formularios
* campos de errores
* html estructural para ayudar a los estilos
* opción <tt>:remote</tt>
* el tema 6 :-)

!SLIDE smbullets incremental
## <tt>number\_to\_currency, price, price=</tt>
* Superman 3 y los medios centavos
* MVP, necesidades/recursos
* guardamos una cosa distinta a la mostrada
* para producción: <https://github.com/RubyMoney/money-rails>

!SLIDE smbullets incremental
## En casa: testeos y validaciones
* ¿Os atreveis a testear el controlador?
* usando los asserts vistos
* testead al menos una de las vistas
* encontrad algo que podría hacer y aun no hace

