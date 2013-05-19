!SLIDE centered
<div class="rails">&nbsp; </div>
# Introducción a Ruby on Rails #
Mayo 2013<br>Fernando Martínez
<br>
<div class="redradix">&nbsp; </div>

!SLIDE bullets incremental
# Parte 1 #

* ¿Qué es Ruby?
* ¿Qué es Rails?
* ¿Que es un framework?
* ¿Qué es una web?
* ¿Qué es una "aplicación"?

!SLIDE smbullets incremental
## Muchas piezas... ##

* _Sistema Operativo:_ OSX, GNU/Linux, Windows
* _Base de Datos:_ SQLite, Mysql, Postgres
* _Editor/IDE:_ Vim, Emacs, Textmate
* _Navegador:_ Safari, Chrome, Firefox
* _Runtime:_ Webrick, Thin, Puma, Passenger, Mongrel2
* _Servidor web:_ Apache, Nginx, IIS
* _Repositorio de código:_ Git, SVN, Mercurial
* _Gestión de bugs:_ Basecamp, GHIssues, ...

!SLIDE bullets incremental
# ¿ Ruby ? ¿ Rails ? #
* Prerequisitos: curl, bash, build-essential...
* Ruby: RVM <https://rvm.io/> (gemsets y .rvmrc)
* Bundler: <http://gembundler.com/>
* ¿Tenemos todo?

!SLIDE subsection
# Ruby para llevar #

!SLIDE commandline incremental
## Hablando con Ruby ##
    $ ruby -e 'print "Hola mundo\n"'
    Hola mundo
    $ irb
    >> "Hola mundo"
    => "Hola mundo"
    >> puts "Hola mundo"
    Hola mundo
    => nil

!SLIDE commandline incremental
## Calculadora gratis! ##

    >> 3+2
    => 5
    >> 3*2
    => 6
    >> 3**2
    => 9
    >> Math.sqrt(9)
    => 3.0

!SLIDE commandline incremental
## Variables ##

    >> a = 3 ** 2
    => 9
    >> b = 4 ** 2
    => 16
    >> Math.sqrt(a+b)
    => 5.0

!SLIDE code
## Métodos ##
    @@@ ruby
    def nombre(parametro1, parametro2 = "por defecto")
      # codigo aquí
      # se devuelve con: return valor
      # o el valor de la última expresión
    end

    # se llaman asi:
    nombre('p1', 'p2')
    nombre('p1')
    nombre 'p1'
    nombre 'p1', 'p2' # cuidado!

!SLIDE commandline incremental
## Métodos ##

    >> def h
    >> puts "Hello World!"
    >> end
    => nil
    >> h()
    Hello World!
    => nil
    >> h
    Hello World!
    => nil

!SLIDE commandline incremental
## Parámetros ##

    >> def h(name = "World")
    >> puts "Hello #{name.capitalize}!"
    >> end
    => nil
    >> h "chris"
    Hello Chris!
    => nil
    >> h
    Hello World!
    => nil

!SLIDE code
## Ejercicio: Nuestro primer acrchivo ruby ##
    @@@ ruby
    # greeter.rb
    class Greeter
      def initialize(name = "World")
        @name = name
      end
      def say_hi
        puts "Hi #{@name}!"
      end
      def say_bye
        puts "Bye #{@name}, come back soon."
      end
    end

!SLIDE commandline incremental
## Usando código de un fichero ##

    # from greeter.rb folder
    $ irb
    >> load 'greeter.rb'
    => true
    >> g = Greeter.new
    => #<Greeter:0x23ef560 @name="World">
    >> g.say_hi
    Hi World!
    => nil

!SLIDE commandline incremental
## cont. ##

    >> g_fer = Greeter.new('Fer')
    => #<Greeter:0x23c7588 @name="Fer">
    >> g_fer.say_hi
    Hi Fer!
    => nil
    >> g_fer.say_bye
    Bye Fer, come back soon.
    => nil

!SLIDE commandline incremental
## los secretos de los objetos... ##

    >> g = Greeter.new("Pat")
    >> g.@name
    SyntaxError: compile error...
    >> Greeter.instance_methods
    => ["method", "send", "object_id", "singleton_methods", "__send__", "equal?", "taint", "frozen?", ...]
    >> Greeter.instance_methods(false)
    => ["say_bye", "say_hi"]

!SLIDE commandline incremental
## objetos cont. ##

    >> g.respond_to?("name")
    => false
    >> g.respond_to?("say_hi")
    => true
    >> g.respond_to?("to_s")
    => true
    # to_s: where does it come from?

!SLIDE commandline incremental
## parcheando... de nuevo ###

    >> class Greeter
    >>   attr_accessor :name
    >> end
    => nil
    >> g = Greeter.new("Andy")
    >> g.respond_to?("name")
    => true
    >> g.respond_to?("name=")
    => true
    >> g.say_hi
    Hi Andy!

!SLIDE commandline incremental
## ¿rayos X? ##

    >> g.name = "Betty"
    => "Betty"
    >> g
    => #<Greeter:0x3c9b0 @name="Betty">
    >> g.name
    => "Betty"
    >> g.say_hi
    Hi Betty
    => nil


!SLIDE bullets incremental
## ¿De qué están hechos los programas? ##
* Datos (variables, classes, objects... )
* Decisiones (conditionals)
* Repetición (loops)

!SLIDE code
## Decisiones / Condicionales ##
    @@@ ruby
    if cond
      #do stuff
    elsif other_cond
      #do other stuff
    end

    action unless condition

    var = condition ? value_if_true : other_value

    condition && value_if_true || other_value
    # vs
    condition and action_if_true or other_action

!SLIDE code
## Repetición / bucles ##
    @@@ ruby
    while conditional [do]
       code
    end

    code while condition

    until conditional [do]
       code
    end

    code until conditional

!SLIDE code
## Más Repeticiones ##
    @@@ ruby
    for n in (1..10) do
      break if n > 7
      next if (n % 3 == 0)
      puts "#{n} > 2 and #{n/3} != 0"
    end

    (1..10).each do |n| # <- this is ruby 'fashion'
      # rewrite yourselves!!
    end
    # Beware: redo, retry
    # read and try examples:
    # http://ruby-doc.org/core-1.9.3/Enumerable.html

!SLIDE commandline incremental
# Ejercicio #
    # Get code
    $ git clone https://gist.github.com/5534016.git ej1
    # Go to part I
    $ cd ej1
    # Open mega_greeter.rb
    # Code until you get sample output :)


!SLIDE subsection
# ¿Por qué ir (o no) en tren? #

!SLIDE bullets incremental
* Rails no es magia
* Rails no vale para todo
* Rails no vale para todos

.notes entonces te pones ha hacer algo y...

!SLIDE bullets incremental

* El Marketing siempre es un poco mentira
* _07/11/2005:_ David Heinemeier Hansson, el creador del framework Ruby on Rails de desarrollo web muestra como crear...
* **[un blog en 15 minutos](http://youtu.be/Gzj723LkRJY)**.

!SLIDE
![Es duro](no_magic.jpg)
## Ami también me j... fastidia##

!SLIDE
![La fama cuesta](fama_profe1.jpg)
## La fama ... CUESTA ##

!SLIDE bullets incremental
#it is **hard**#
* <http://goo.gl/LDJjE><br>
(2012-05-08: Kakubei «Why I hate Rails»)
* But gets throught!

!SLIDE bullets incremental
Hay alternativas

* Blog -> Wordpress
* CMS -> Drupal
* Office + Intranet -> Sharepoint
* Web Evento -> jekyll

!SLIDE subsection
# un ¿_framework_? #

!SLIDE bullets incremental
## garabateando servilletas ##
* Why the lucky stiff (\_why)
* Camping (the Microframework)
* Sinatra (aka Rails lil' cousin)
* Example REST MVC: <http://goo.gl/q4xfi>
* Let's read/play together!

!SLIDE commandline incremental
# Ejercicio #
    $ git clone https://gist.github.com/5534133.git ej2
    $ cd ej2
    $ rvm gemset use --create ruby-1.9.3@sinatra
    $ bundle
    Fetching gem metadata from https://rubygems.org/.........
    $ ruby todo.rb
    $ browser localhost:4567
    # Jugad!

!SLIDE bullets incremental
## Clase de san Isidro ##
* por favor votad
* esta en vuestro correo
* <http://www.doodle.com/rcc97n2vz38exv64>
* si alguien no puede en absoluto...
* ... la haremos el lunes
* diapos del tema 1: <http://j.mp/rr_slides>

!SLIDE bullets incremental
* Ruby on Rails is an open-source web framework
that's optimized for programmer happiness
and sustainable productivity.
<br>It lets you write beautiful code
by favoring convention over configuration.
* Bla bla bla, veamos:

!SLIDE
**Ruby** on Rails is an open-source web framework
<br> that's optimized for **programmer happiness**
<br>and sustainable productivity.
<br>It lets you write beautiful code
<br>by favoring convention over configuration.

!SLIDE bullets incremental
## en el prinicipio fue Ruby ##
* Dynamic
* Orientado a Objetos (_realmente_)
* Open-source (de nuevo)
* Simple (esconde la complejidad)
* Elegante (Japón, koans, Heroku)
* "Feliz"

!SLIDE
Ruby on Rails is an **open-source** web framework
<br> that's optimized for programmer happiness
<br>and sustainable productivity.
<br>It lets you write beautiful code
<br>by favoring convention over configuration.

!SLIDE bullets incremental
## una cultura _opensource_ ##
* Rubygems (CPAN, PEAR)
* OS & tools, not products (Rails vs Basecamp)
* Git & Github!

!SLIDE
Ruby on Rails is an open-source **web** framework
<br> that's optimized for programmer happiness
<br>and sustainable productivity.
<br>It lets you write beautiful **code**
<br>by favoring convention over configuration.

!SLIDE smbullets incremental
## ¿Que es una «web»? ##
* ¿HTML, Browser?
* ¿Js?
* ¿API?

## ¿Qué es un «app»? ##
* ¿Datos?
* ¿Función?
* ¿UX?

!SLIDE
Ruby on Rails is an open-source web **framework**
<br> that's optimized for programmer happiness
<br>and sustainable productivity.
<br>It lets you write beautiful code
<br>by favoring convention over configuration.

!SLIDE smbullets incremental
## <https://es.wikipedia.org/wiki/Framework> ##
* un conjunto estandarizado de
* conceptos, prácticas y criterios
* enfocados a resolver
* un tipo particular de problema
* que se usa como referencia,
* para enfrentar y resolver
* problemas nuevos de índole similar.

!SLIDE bullets incremental
* estandarizado -> CoC
* conceptos -> MVC, REST, UJS
* prácticas -> TDD/BDD...
* criterios -> REST, naming, DRY
* tipo particular de problema:
* **aplicación web con BBDD**
