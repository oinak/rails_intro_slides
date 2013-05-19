!SLIDE centered
<div class="rails">&nbsp; </div>
# Introducción a Ruby on Rails #
# 7 y 8 - Consejos para Viajes complicados #
Mayo 2013
<br>Fernando Martínez
<br>
<div class="redradix">&nbsp; </div>

!SLIDE subsection
# Parte 7 #

!SLIDE smbullets incremental
## la Web - entorno abierto
* hay que desconfiar :'(
* sanitizar entradas...
* ... y salidas (<tt>h()</tt> y <tt>raw()</tt>)
* y restringir

!SLIDE smbullets incremental
# Autentificación:
* hubo una vez que se hacía a mano
* 30-8-2006: [Dave Astels vs Rails Recipes](http://techblog.daveastels.com/2006/08/30/role-based-authentication-from-rails-recipes-part-1/)
* mucho ha llovido, ahora tenemos [Devise](http://devise.plataformatec.com.br/) peeero
* Ctrl-F -> "Starting with Rails?"
* ¿entonces? por ejemplo [Authlogic](https://github.com/binarylogic/authlogic#readme)

!SLIDE smbullets
## Ejercicio
* En el repo teneis: [Authlogic de corta-pega](http://railscasts.com/episodes/160-authlogic?view=asciicast)
* El que dude, que lo siga (ojo rails 2) hasta quedar igual
* El que se atreva que vaya a por la [Versión moderna](<http://saravani.wordpress.com/2012/01/03/rails-3-authlogic-implementation-a-basic-tutorial)
* proteged acciones delicadas con <tt>required\_user</tt>
* ¿y si hay distintos tipos de usuarios?

!SLIDE
# ¿y que más?
* recuperar contraseña por mail
* activar, bloquear usuarios
* limitar intentos de acceso
* recordar al usuario
* por todo eso existe Devise

!SLIDE smbullets incremental
# Autorización/permisos/roles
* hay conocidos y conocidos...
* el cartero puede repartir tu correo...
* ...pero no leerlo
* ¿Quien puede hacer qué? (pensando en REST)
* [CanCan](https://github.com/ryanb/cancan#readme)
* Compatible con Devise y Authlogic

!SLIDE code incremental
## ...un botón
    @@@ ruby
    <% if can? :update, @article %>
      <%= link_to "Edit", edit_article_path(@article) %>
    <% end %>

    class ArticlesController < ApplicationController
      load_and_authorize_resource

      def show
        # @article is already loaded and authorized
      end
    end

!SLIDE subsection
# Parte 8 #

!SLIDE smbullets incremental
# Consejos de viaje
* Aprovecha el Duty Free: ActiveSupport
* Lleva un diccionario: Traducción y localización
* Viaja ligero: responsive web design y mobile first
* Frameworks de diseño: Bootstrap, Foundation, 960.gs

!SLIDE commandline incremental
## Aprovecha el Duty Free: ActiveSupport
    $ @var.blank?   # @var.nil? || @bar.empty?
    $ @var.present? # !@var.blank?

    $ @var.presence # @var.present ? @var : nil
    $ host = config[:host].presence || 'localhost'

!SLIDE commandline incremental
## ActiveSupport - 2
    $ @bar.try(:wadus,1) # @bar.nil? ? nil : @bar.wadus(1)
    $ @person.try { |p| "#{p.first_name} #{p.last_name}" }

    $ 1.in?(1,2) # => true
    $ alias_attribute :login, :email
    $ attr_accessor_with_default :port, 80
    $ delegate :attr_name, :to => :asocc_name

!SLIDE commandline incremental
## ActiveSupport - 3
    $ "".html_safe? # => false
    $ s = "<script>...</script>".html_safe
    $ s.html_safe? # => true
    $ s            # => "<script>...</script>"
    $ # no comprueba, solo recuerda

!SLIDE smbullets incremental
## y la magia de nombres
* truncate
* pluralize, singularize, humanize, foering\_key
* camelize, titleize, underscore, dasherize
* parametrize, tableize, classify, constantize
* etc... [ActiveSupport CoreExtensions](http://guides.rubyonrails.org/active_support_core_extensions.html)

.notes mirar lo que le hace a las clases de tipos de Ruby


!SLIDE smbullets incremental
## Lleva un diccionario: Traducción y localización

* es dificil explicarlo mejor que:
* <http://guides.rubyonrails.org/i18n.html>
* sí, es un peñazo
* sí, es mucho trabajo extra
* hacedlo si os reporta beneficio
* desde el principio o muy difícil

!SLIDE code
## Ejemplo - I18n, vistas
    @@@ ruby
    # config/locales/es.yml
    es:
      courses:
        index:
          title: "Título"

    # app/views/courses/index.html.erb
    <%= t '.title' %>

!SLIDE code
## Ejemplo - I18n
    @@@ ruby
    # config/locales/es.yml
    es:
      activerecord:
        models:
          course: Curso
        attributes:
          course:
            title: "Título" # las comillas conservan el acento

    # app/views/courses/index.html.erb
    ...
    <th><%= Course.human_attribute_name("title") %></th>
    ...

!SLIDE smbullets incremental
## No todo es horror
* Rails trae mucho hecho
* Mecanismo MUY completo: plurales, modelos, interpolación
* gema con traducciones: [rails-i18n](https://github.com/svenfuchs/rails-i18n)
* Errores, fechas, formatos en español: <http://goo.gl/npdIC>
* integración con muchas gemas como Devise
* Contenido: otra historia [Globalize](http://railscasts.com/episodes/338-globalize3?language=es&view=asciicast)

!SLIDE smbullets incremental
## Viaja ligero: responsive web design y mobile first
* no voy a comisión ni nada pero:
* [ABA: Mobile First](http://www.abookapart.com/products/mobile-first)
* [ABA: Responsive Web Design](http://www.abookapart.com/products/responsive-web-design)
* como la traducción: desde el principio mas fácil

!SLIDE smbullets incremental
## Frameworks de diseño:
* El rey: [Twitter Bootstrap](http://twitter.github.io/bootstrap/)<br>
  <span style="font-size:90%">gema, generators, SimpleForm</span>
* [Foundation](http://foundation.zurb.com/) dice ser _mobile first_
* [960.gs](https://github.com/hauleth/sass-960gs) famoso
* [Bourbon](http://bourbon.io/) Sass mixin, ligero
* [HTML KickStart](http://www.99lime.com/elements/)

!SLIDE smbullets incremental
## Frameworks de diseño - cont
* elegir al principio
* preguntad a vuestro frontend-person
* personalizar por override = actualizable
* ojo con Sprockets

!SLIDE subsection
# Epílogo #

!SLIDE smbullets incremental
## Para empezar:
* [Aprended Ruby](http://ruby.learncodethehardway.org/)
* [Agile Web Development with Rails 4](http://pragprog.com/book/rails4/agile-web-development-with-rails-4) ebook y papel: $$$
* [Rails Tutorial Rails](http://ruby.railstutorial.org/ruby-on-rails-tutorial-book?version=4.0) gratis, papel y videos: $$$
* [Rails for Zombies](http://railsforzombies.org/) gratis, continuaciones de pago

!SLIDE smbullets incremental
## Para mejorar:
* [Ruby Koans](http://rubykoans.com/) Aprender ruby arreglando tests
* [The Rails 4 Way](https://leanpub.com/tr4w)
* [Rails Recices](http://pragprog.com/book/rr2/rails-recipes)
* [Rails Best Practices](http://rails-bestpractices.com/)

!SLIDE smbullets incremental
## para petarlo
* [Metaprogramming Ruby] (http://pragprog.com/book/ppmetr/metaprogramming-ruby) de Paolo Perrotta
* [Blog de Aaron Patterson](http://tenderlovemaking.com/)
