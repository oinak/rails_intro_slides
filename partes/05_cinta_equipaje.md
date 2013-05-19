!SLIDE centered
<div class="rails">&nbsp; </div>
# Introducción a Ruby on Rails #
# 5 - La cinta de equipaje #
Mayo 2013
<br>Fernando Martínez
<br>
<div class="redradix">&nbsp; </div>

!SLIDE subsection
# Parte 5 #

!SLIDE bullets incremental
## veremos:##
* cosas de la BD
* otras cosas que se guardan

!SLIDE smbullets incremental
## más alla de <tt>first</tt>, <tt>last</tt> y <tt>all</tt>##
* joins
* scopes, condition override
* dinamic\_finders
* calculations

!SLIDE code
    @@@ ruby
    class Category < ActiveRecord::Base
      has_many :products
    end
    class Product < ActiveRecord::Base
      belongs_to :category
      has_many :comments
      has_many :tag
    end
    class Comment < ActiveRecord::Base
      belongs_to :product
      has_one :guest
    end
    class Guest < ActiveRecord::Base
      belongs_to :comment
    end
    class Tag < ActiveRecord::Base
      belongs_to :product
    end

!SLIDE commandline incremental
## joins automágicos ##
    >> Category.joins(:products)
    SELECT categories.* FROM categories
    INNER JOIN products ON products.category_id = categories.id

    >> Product.joins(:category, :comments)
    SELECT products.* FROM products
    INNER JOIN categories ON products.category_id = categories.id
    INNER JOIN comments ON comments.product_id = products.id

    >> Product.joins(:comments => :guest)
    SELECT products.* FROM products
    INNER JOIN comments ON comments.product_id = products.id
    INNER JOIN guests ON guests.comment_id = comments.id

!SLIDE commandline incremental
## triple salto mortal ##
    >> Category.joins(:products => [{:comments => :guest}, :tags])

    SELECT categories.* FROM categories

    INNER JOIN products ON products.category_id = categories.id

    INNER JOIN comments ON comments.product_id = products.id

    INNER JOIN guests ON guests.comment_id = comments.id

    INNER JOIN tags ON tags.product_id = products.id

!SLIDE smbullets incremental
## acerca de los _joins_ ##
* se puede limitar, con <tt>where</tt>
* <tt>.joins</tt> sólo hace _inner joins_
* (aunque hay una forma de hacer _outer_)
* N+1!

!SLIDE commandline incremental
## ¿ N+1 ? ##
    >> clients = Client.includes(:address).limit(10)
    >> clients.each do |client|
    >>   puts client.address.postcode
    >> end
    11 peticiones, a la BD, con sus viajes de red

    >> Category.includes(:posts => [{:comments => :guest}, :tags]).find(1)
    2 peticiones

!SLIDE smbullets incremental
# scopes #
* Cuando alguna de estas peticiones se reutiliza
* o la abstraeriamos en un metodo
* hacemos un _scope_
* <tt>scope :published, where(:published => true)</tt>
* <tt>scope :last\_week, lambda { where("created\_at < ?", Time.zone.now ) }</tt>

.notes el lambda en inprescindible para que Time.now se ejecute cuando la consulta

!SLIDE code
## scopes con argumento ##
    @@@ ruby
    class Edition < ActiveRecord::Base
      scope :starting_soon, lambda { |date|
        where("start < ?", date)
      }
    end

!SLIDE smbullets incremental
## Ejercicio 1 ##
* un scope acepte argumentos o no
* no _nil_ sino ser llamado _sin_ argumentos
* Edition.starting_soon
* Edition.starting_soon(1.week.since(Time.now) )
* ¿podeis encontrarlo?

!SLIDE commandline incremental
## salida deseada ##
    >> Edition.soon(1.day.since(Time.now))
    Edition Load (0.5ms)  SELECT "editions".* FROM "editions" WHERE (start < '2013-05-07 14:53:44.752681')
    => [#<Edition id: 1...]
    >> Edition.soon
    Edition Load (0.5ms)  SELECT "editions".* FROM "editions" WHERE (start < '2013-05-13 14:53:47.827892')
    => []

!SLIDE commandline incremental
## solución ##
    >> class Edition < ActiveRecord::Base
    >> scope :soon, lambda {|*args|
    >>   where( "start < ?",
    >>          (args && args.first ? args.first : 1.week.since(Time.now)))}
    >> end

.notes aplicaciones como busquedas avanzadas multi filtro

!SLIDE smbullets incremental
## Ejercicio 2 ##
* revisar el código de la aplicación
* localizar puntos de aprovechamiento
* agregar un <tt>joins</tt>, un <tt>includes</tt> y un <tt>scope</tt>
* ¿se testea?, ¿a que nivel?

!SLIDE smbullets
## ayudas ##
* en la app de ejemplo:
* <tt>link\_to\_unless</tt>
* Courses filtrados con scope
* params no REST

!SLIDE subsection
# Dinamic finders #

!SLIDE smbullets incremental
## dinamic finders ##
* Model.find\_by\_foo\_and\_bar
* Model.find\_all\_by\_foo
* Model.find\_last\_by\_foo
* Model.where(...).first\_or\_create(atts)
* Model.where(...).first\_or\_initialize()

!SLIDE code
## pluck ##
    @@@ ruby

    Edition.pluck(:id) # en vez de: Edition.select(:id).map{|e| e.id}

    Edition.where(:active => true).pluck(:id)
    # SELECT id FROM editions WHERE active = 1

    Edition.uniq.pluck(:location)
    # SELECT DISTINCT location FROM editions
    # por ejemplo para sugerir locations en el form

!SLIDE subsection
# Calculations #

!SLIDE code
# contar #
    @@@ruby
    Client.count
    # SELECT count(*) AS count_all FROM clients

    Client.where(:first_name => 'Ryan').count
    # SELECT count(*) AS count_all FROM clients WHERE (first_name = 'Ryan')

!SLIDE code
# contar 'pro'#
    @@@ruby
    Client.includes("orders").where( :first_name => 'Ryan',
                                     :orders => {:status => 'received'}).count

    # SELECT count(DISTINCT clients.id) AS count_all FROM clients
    #  LEFT OUTER JOIN orders ON orders.client_id = client.id WHERE
    #  (clients.first_name = 'Ryan' AND orders.status = 'received')

!SLIDE smbullets
# ¿que tenemos? #
* **Comodos:**
* minimun
* maximum
* sum
* **Versátil**
* calculate


!SLIDE smbullets
# ¿y si ActiveRecord 'arrastra' ?#
* si las consultas van lentas...
* usar explain
* que puede ser automático:
* <tt style="font-size:80%">config.active\_record.auto\_explain\_threshold\_in\_seconds</tt>
* y varía segun el motor de BD

!SLIDE subsection
# Cache #

!SLIDE smbullets incremental
## ¿caché o qué?
* aquí iba un rollo sobre las caches en Rails
* las viejas: <http://guides.rubyonrails.org/caching_with_rails.html>
* pero en caché, Rails 4 _lo peta_
* Resúmen: <http://goo.gl/CRXub>
* Buscad a david: (en unos días) <http://confreaks.com/events/railsconf2013>
