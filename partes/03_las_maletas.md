!SLIDE centered
<div class="rails">&nbsp; </div>
# Introducción a Ruby on Rails#
## 3 - Las Maletas ##
Mayo 2013
<br>Fernando Martínez
<br>
<div class="redradix">&nbsp; </div>

!SLIDE bullets incremental
# Parte 3 #
## Las Maletas ##
* Bases de Datos
* Evolución de la estructura (migraciones)
* Consultas _sin_ SQL
* Validación de datos
* Reacciones (callbacks)

!SLIDE subsection
# Modelos #

!SLIDE bullets incremental
## ¿Que son los modelos?
* ¿Señoras flacas + ropa cara?
* Datos
* Relaciones
* Comportamiento

!SLIDE bullets incremental
## ¿Aplicaciones y datos?
* ¿Que son los datos?
* ¿Aplicaciones sin datos?
* ¿Datos sin aplicaciones?
* Persistencia

.notes Desafiar a buscar ejemplos

!SLIDE bullets incremental
## Modelos en Rails ##
* Migraciones
* Validaicones y Callbacks
* Asociaciones
* Consultas

.notes en este orden oara irlo haciendo en la app

!SLIDE subsection
# Migraciones #

!SLIDE code
## Migraciones: ¿qué aspecto tienen?
    @@@ ruby
    class CreateCourses < ActiveRecord::Migration
      def up
        create_table :courses do |t|
          t.string :name, :null => false
          t.text :description
          t.timestamps # <- no es un tipo!
        end
      end

      def down
        drop_table :courses
      end
    end

!SLIDE smbullets incremental
## Migraciones: ¿qué se puede hacer?
* up
* down
* change

!SLIDE smbullets incremental
## Migraciones *change*: ##
* add_column
* add_index
* add_timestamps
* create_table
* remove_timestamps
* rename_column
* rename_index
* rename_table

!SLIDE smbullets incremental
## Migraciones *sólo up/down*: ##

* change_table
* change_column
* drop_table
* remove_column
* remove_index


!SLIDE bullets incremental
## Migraciones *qué dicen*: ##
* say
* say\_with\_time
* suppress_warnings{}

Algo más? <http://api.rubyonrails.org/classes/ActiveRecord/Migration.html>


!SLIDE commandline incremental
## Migraciones: ¿como se usan?
    $ rake --tasks db
    ...
    rake db:migrate         # Migrate the database (options: VERSION=x, VERBOSE=false).
    rake db:migrate:status  # Display status of migrations
    rake db:rollback        # Rolls the schema back to the previous version (specify steps w/ STEP=n).
    ...

!SLIDE code
## Migraciones: peligros
    @@@ ruby
    # db/migrate/20100513121110_add_active_to_course.rb
    class AddActiveToCourse < ActiveRecord::Migration
      def change
        add_column :courses, :active, :boolean
        Course.all.each{ |c| c.update_attributes!(:active => true) }
      end
    end
    # app/model/course.rb
    class Course < ActiveRecord::Base
      validates :active, :presence => true
    end

!SLIDE code
## Migraciones: peligros
    @@@ ruby
    # db/migrate/20100515121110_add_loc_to_product.rb
    class AddLocationToCourse < ActiveRecord::Migration
      def change
        add_column :courses, :location, :string
        Course.reset_column_information
        Course.all.{ |c| course.update_attribute(:location, 'Madrid')}
        # Course.update_all no valida y podria dejar datos no validos!
      end
    end
    # app/model/course.rb
    class Course < ActiveRecord::Base
      validates :active, :presence => true
      validates :location, :presence => true
    end

!SLIDE bullets incremental
## Migraciones: peligros
<tt style="color:red">undefined method `location' for #&lt;Course...&gt;</tt>

* ¿Como lo evitamos?
* No siempre igual
* Validación condicional (se queda!)
* Modelo mínimo dentro de la migración
* Valores por defecto en vez de inicializar

!SLIDE smbullets incremental
## Migraciones: ejercicio  ##
* crea <tt>Course</tt> con <tt>name, desc, timestamps</tt>
* Ejecuta ida y vuelta (<tt>rake --tasks</tt>)
* añade <tt>active</tt> (bool), ajustado a _true_ por defecto
* Agrega al menos una validación
* Migra de nuevo

!SLIDE subsection
# Validaciones #

!SLIDE bullets incremental
## Validaciones: por qué ##
* **Convención:** validar en ruby, no en db
* No guardar datos incorrectos
* No depender de la implementación BBDD
* Mejores (mágicos) mensajes de error

.notes rails opinión: asoc y validaciones en ruby

!SLIDE smbullets incremental
## sí validan: ##
* create
* create!
* save *
* save!
* update
* update_attributes
* update_attributes!

.notes save puede no validar si se le especifica por opción

!SLIDE smbullets incremental
## no validan: ##
* decrement!
* decrement_counter
* increment!
* increment_counter
* toggle!
* touch
* update_all
* **update_attribute**
* update_column
* update_counters

!SLIDE commandline incremental
## validando 'a mano' ##
    >> course.valid?
    >> course.invalid?
    provocan validaciones
    rellenan model.errors
    devuelven true/false

    >> course.errors
    => {:field => ["can't be something"], ...}

    >> Course.new.errors[:name].any?
    => false
    >> Course.create.errors[:name].any?
    => true

!SLIDE bullets incremental
## Validaciones: acceptance ##
    @@@ ruby
    validates :terms_of_service, :acceptance => true
* Para usar ckeck box (lo veremos)
* ¿Es imprescindible salvar?
* Opción :on (:create, :update, :save)
* ¿Cuando? ¿Ejemplos?

!SLIDE bullets incremental
## associated ##
    @@@ ruby
    belongs_to :course # veremos 'belongs_to' T.4
    validates_associated :course, :if => :course_id
    # sólo en un lado de la asociacion!

    validates :course_id, :presence => true

* comprueba validez, no presencia

!SLIDE code
## confirmation ##
    @@@ruby
    validates :email, :confirmation => true

    # En la vista hay dos campos en vez de uno:

    <%= text_field :user, :email %>
    <%= text_field :user, :email_confirmation %>

    # Sólo se mira si hay field_confirmation

    validates :email_confirmation, :presence => true


!SLIDE code
## inclusion ##
    @@@ruby
    validates :size, :inclusion => { :in => 1..12,
        :message => "%{value} is not a valid month" }

La lista dada a <tt>:in</tt> es cualquier cosa que responda a <tt>.include?</tt>
<tt>Array, Range, String*...</tt>

<http://ruby-doc.org/core-1.9.3/Enumerable.html#method-i-include-3F>

Tiene un gemelo malvado llamado...

!SLIDE code
... en un giro sorprendente de los acontecimientos:
## exclusion ##
    @@@ruby
    validates :number, :exclusion => { :in => 1..3,
        :message => "%{value} is not a basket player number" }

!SLIDE smbullets incremental
## format ##
    @@@ruby
    validates :email,
              :format => { :with => /\A.+@\w+\.+\w+\z/,
              :message => "not a valid email" }

* **Usos:**
* Correo
* Nombres de usuario
* DNI/NIE
* Contraseñas (¡Ojo!)
* Códigos postales
* Teléfonos

!SLIDE code
## Custom: validates_with ##
Proveemos una _clase validadora_

    @@@ ruby
    class Person < ActiveRecord::Base
      validates_with GoodnessValidator
    end
    class GoodnessValidator < ActiveModel::Validator
      def validate(record)
        if record.first_name == "Evil"
          # errores al modelo entero, no a un atributo
          record.errors[:base] << "This person is evil"
        end
      end
    end

!SLIDE code
## Custom: validates_each ##
Hacemos un bloque

    @@@ ruby
    class Person < ActiveRecord::Base
      validates_each :name, :surname do |record, attr, value|
        msg = 'must start with upper case'
        record.errors.add(attr, msg) if value =~ /\A[a-z]/
      end
    end

!SLIDE smbullets
## Ejercicio ##
* Generar un scaffold de <tt>Course</tt>
* Validar que haya nombre
* Arrancar rails mostrando los cursos
* Dar un curso de alta (sin nombre)
* Modificarlo, Borrarlo
* ¿Preguntas?

.notes ¿que pasa con el modelo/migración que ya teníamos?

!SLIDE commandline incremental
## ayudas ##
    $ rails g scaffold Course name:string ... -s
    invoke  active_record
    skip    db/migrate/20130422213321_create_courses.rb
    skip    app/models/course.rb
    $ rails s ... Ctrl-C
    $ rm public/index.html
    $ edit  config/routes.rb
    # root :to => 'courses#index'
    $ rails s
    $ browser localhost:3000

.notes -s salta, -f fuerza, rails g <cosa>

!SLIDE subsection
# Tests #

!SLIDE bullets incremental
* ¿Test Driven Development?
* ¿Por qué probar? El precio de la seguridad
* <http://rubykoans.com/>
* Entornos de Rails:<br>
producción, desarrollo y pruebas
* Tecnologías de test:<br>rspec, cucumber...
* Mocking y Stubbing

!SLIDE bullets incremental
# lo que rails trae #
* Estructura preparada<br>unit, functional, integration, benchmark
* Base de datos separada
* Helper específicos
* Fixtures (datos de prueba)
* Tareas rake (para ejecutarlos)

!SLIDE smbullets incremental
# Testeos Unitarios #
* Comprueban el modelo/objeto aislado
* No probamos que AR funciona
* Pueden hacerse con la _BD en memoria_
* Pueden hacerse _sin BD_ pero
* Buen sitio para probar especificidades BD
* ¡Principio de Demeter!
* Demeter y los Mocks/Stubs

!SLIDE smbullets incremental
## ¿cómo se testea ? ##
* Testeamos mediante aserciones
* son como alarmas
* "pasa esto, o me avisas"
* <tt>assert_something esperado, testeado, mensaje </tt>
* veamos algunas de los helpers

!SLIDE code
## ¿existe? ¿es cierto?
    @@@ ruby
    assert bool_val, [msg]

    course = Course.new
    assert course.active?, "debe estar activo por defecto"

    assert_nil obj, [msg]      # obj.nil?

    assert_not_nil obj, [msg]  # !obj.nil?

!SLIDE code
## ¿es lo mismo? ##
    @@@ ruby
    assert_equal obj1, obj2, [msg] # testing for '=='
    assert_not_equal obj1, obj2, [msg]
    assert_same obj1, obj2, [msg] # testing for '.equal?'
    assert_not_same obj1, obj2, [msg]

    > 1 == 1.0        # => true
    > 1.equal?(1.0)   # => false
    > "a" == "a"      # => true
    > "a".equal? "a"  # => false
    > :a.equal? :a    # => true
    > c1, c2 = Course.last, Course.last
    > c1 == c2 # => true
    > c1.equal? c2 # => false

.notes > c1 = c2 = Course.last; c1.equal?(c2)

!SLIDE code
## ¿encaja? ##
    @@@ ruby
    # en la Regexp: %r{r}.match(s) , /r/ =~ s
    assert_match regexp,string,[msg]
    assert_no_match regexp,string,[m]

.notes Regexp, Range

!SLIDE code
## ¿casca? ##
    @@@ ruby
    assert_throws( symbol, [msg] ) { block }
    assert_raise( exception1, ... ) { block }

!SLIDE smbullets incremental
## Ejercicio: testeos unitarios ##
* Escribir tests/migraciones para Course:
* Título no puede estar vacío
* Activo no puede ser nulo
* Fecha debe ser "futura" si está activo
* Extra:
* Completar el formulario y probar validaciones
<tt>check_box, date_select</tt>

!SLIDE code
## Ejercicio: ayudas ##
    @@@ ruby
    # test/unit/course_test.rb
    require 'test_helper'
    class CourseTest < ActiveSupport::TestCase
     test "a course without title" do
       @course = Course.new
       assert !@course.valid?, "can't be valid"
       assert_equal false, @course.save, "must not save"
       assert_raise(ActiveRecord::RecordInvalid){ @course.save! }
     end
    end
    # En la consola:
    # ruby -Itest test/unit/course_test.rb<br>
    # rake test:units

!SLIDE bullets incremental
# Fixtures #
* Valores que se cargan en la BD de pruebas
* Representan un 'estado inicial'
* Pueden usarse juntas o separadas
* Escalan mal con la complejidad
* Alternativas: Machinist, Factory Girl...

!SLIDE bullets incremental
# Más alla de los unitarios #
* functional -> controlador
* integration -> caso de uso
* performance &rarr; carga/respuesta

!SLIDE bullets
# Aserciones de Rails #
* Para testear cosa 'web'
* se usan en funcionales/integración

!SLIDE code
    @@@ ruby

    assert_valid(record) # depreca a -> assert record.valid?

    assert_difference(expressions, difference = 1, message = nil) {...}
    assert_no_difference(expressions, message = nil, &block)

    assert_difference 'Course.count' do
      post :create, :course => {...}
    end

!SLIDE code
    @@@ ruby

    # genera y reconoce tal ruta
    assert_recognizes(expected_options, path, extras={}, message=nil)
    assert_generates(expected_path, options, defaults={}, extras = {}, message=nil)

    # es un 200 OK ?, un 302 redirect
    assert_response(type, message = nil)
    assert_redirected_to(options = {}, message=nil)

    # usa tal plantilla/vista
    assert_template(expected = nil, message=nil)

!SLIDE bullets incremental
## en el tintero/para ampliar ##
* mocking/stubs: tras asociaciones

Mejor despues del curso / 1ª app:

* <http://blowmage.com/minitest-rails/>
* <http://rspec.info/>
* <http://www.fastrailstests.com/>

.notes minitest viene en ruby 1.9+, rspec es de lo mas popular

!SLIDE subsection
# Consultas #

!SLIDE
Ya sé que estaba en el tema 5 del índice, pero es dificil seguir sin verlo

!SLIDE smbullets incremental
## ¿por qué no SQL? ##
* DRY
* DB agnóstico
* tipos Ruby
* funcionalidad extra en modelos
* relaciones
* consultas dinámicas
* evaluacion perezosa

!SLIDE code
# find #
    @@@ ruby
    Model.find( 1 )

    # SELECT * FROM clients WHERE (clients.id = 10) LIMIT 1

    Model.find( [1,3,7] )

    # SELECT * FROM clients WHERE (clients.id IN (1,3,7))
    # ActiveRecord::RecordNotFound si no vienen todos

!SLIDE code
# first, last #
    @@@ ruby
    Model.first
    # SELECT * FROM clients LIMIT 1

    Model.last
    # SELECT * FROM clients ORDER BY clients.id DESC LIMIT 1

    Model.last(10)
    # SELECT * FROM clients ORDER BY clients.id DESC LIMIT 10

    # first! y last! -> raise RecordNotFound

!SLIDE code
# where + string #
    @@@ ruby
    # Rails guarda en params los parametros de la peticion HTTP

    Course.where("location = #{params[:location]}") # NO! SQLi

    Course.where("location = ?", params[:location])

    Course.where(" location = ? AND start_date > ?", [loc, date] )

    Course.where("start_date <= :start", {:start => Date.today})

!SLIDE code
# where + hash #
Válido para algunas consultas

    @@@ ruby
    Course.where(:active => true) # ... WHERE active = 1

    # yday = (Time.now.midnight - 1.day)..Time.now.midnight
    Course.where(:created_at => yday)
    # ... created_at BETWEEN '2013-05-06' 00:00:00 
    # AND '2013-05-07' 00:00:00

    Course.where(:location => ['Madrid','Barcelona'])

!SLIDE code
# order #
Especifica el orden, pueden ser varios, cuidado con ambigüedad

    @@@ruby
    Course.order(:created_at)
    Course.order("created_at")
    Course.order("created_at DESC")
    Course.order("created_at ASC")

!SLIDE code
# select #
    @@@ ruby
    Course.select("location")
    # retorna objetos de sólo lectura
    # para qué

    Course.select("location").uniq
    # por ejemplo para sugerir en un combo

!SLIDE smbullets incremental
# Ejercicio #
* "filtros" en index
* courses#index muestra solo activos
* cursos salen en orden anticronológico
* completar formularios
* ver consultas en log
* ver dev-tools/firebug
* railspanel...

!SLIDE bullets
## el resto? ##
* Hay más...
* ...en el tema 5

