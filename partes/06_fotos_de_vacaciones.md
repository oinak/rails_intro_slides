!SLIDE centered
<div class="rails">&nbsp; </div>
# Introducción a Ruby on Rails #
# 6 - Fotos de Vacaciones #
Mayo 2013
<br>Fernando Martínez
<br>
<div class="redradix">&nbsp; </div>

!SLIDE subsection
# Parte 6 #

!SLIDE smbullets incremental
# V de Vendetta, o de Vista
* Layouts, vistas y parcial
* Motores de rendering
* Recursos y representaciones
* Helpers y form helpers
* UI Frameworks & kits (T.8)

!SLIDE smbullets incremental
## Layouts, vistas y parcial
* Layout: repetición externa <tt>application.erb</tt>
* Vista: representacion del recurso <tt>new.erb</tt>
* Partial: repetición interna (plantilla) <tt>\_form.erb</tt>
* Helper: repetición interna (código) <tt>link\_to</tt>

!SLIDE smbullets incremental
## Layout
* Ficheros en: <tt>app/views/layouts</tt>
* Por defecto: <tt>application.html.erb</tt>
* A nivel controlador: *método*<br><tt>layout "admin"</tt>
* A nivel acción: *opción*<br><tt>render :layout => false</tt>
* Para más 'rellenos':<br><tt>content\_for :wadus</tt>

!SLIDE smbullets incremental
## Vista
* en <tt>app/views/{controller}/{accion}.{motor}</tt>
* <tt>render "edit"</tt> en update sólo cambia de acción
* entra en el <tt>yield</tt> del _layout_
* recibe las variables de instancia <tt>@cosa</tt> de la acción
* llama a _partials_ y _helpers_

!SLIDE smbullets incremental
## Vista o qué
* se pueden renderizar:
* <tt>:nothing</tt> cabeceras, status, sin body
* <tt>:inline</tt> AKA plantilla entre comillas (*)
* <tt>:template</tt> "controlador/accion", no ejecuta la otra acción
* <tt>:file</tt> ruta absoluta, fichero literal
* <tt>:text</tt> llamadas AJAX - html
* <tt>:builder</tt> llama a <tt>.to_xml</tt>
* <tt>:json</tt> [JSON](https://es.wikipedia.org/wiki/JSON) llama a <tt>.to_json</tt>
* <tt>:js</tt> javascript 'en seco'

.notes (*) poco MVC, cómodo para pruebas rápidas

!SLIDE smbullets incremental
## uso avanzado
* el camino _default_ es _casi_ automatico
* para todo lo demás
* por ejemplo <tt>:status</tt> (esencial para ajax)
* <http://apidock.com/rails/ActionController/Base/render>

!SLIDE smbullets incremental
## Partial
* en <tt>app/views/{controller}/\_{nombre}.{motor}</tt>
* un trozo que aparece en varias vistas
* o en una repetidamente
* variable 'mágica' con nombre fichero
* se asigna con <tt>object: @post</tt> o <tt>collection: @post.comments</tt>
* <tt>collection</tt> itera tácitamente con <br><tt>@col.each do |nombre\_partial|</tt>

!SLIDE smbullets incremental
# motores de rendering
* ERB (html,css,js)
* Haml/Sass/Scss <br><http://haml.info/> | <http://sass-lang.com/>
* Slim <http://slim-lang.com/>
* Builder (xml) Atom/Feed...

!SLIDE smbullets incremental
# ERB #
    @@@ html
    <% Ruby code -- inline with output %>
    <%= Ruby expression -- replace with result %>
    <%# comment -- ignored -- useful in testing (*) %>
    <%% or %%> -- replace with <% or %> respectively

* Viene con Ruby
* Default en Rails
* Fácil desde PHP, ASP...

!SLIDE smbullets incremental
# Recursos y Representaciones
* ¿que dice REST?
* Recurso != representacion
* <tt>/courses/1</tt> **no** es @course
* ejemplo blog/feed
* <http://weblog.rubyonrails.org/>
* <http://weblog.rubyonrails.org/feed/atom.xml>

!SLIDE smbullets incremental
# Helpers
* **helper:** función auxiliar
* abstrae comportamiento reutilizable
* relativo a la vista/representacion
* vienen muchos en Rails <tt>ActionView::Helpers</tt>

!SLIDE smbullets incremental
# Helpers generales
* <tt>stylesheet\_link\_tag </tt>
* <tt>javascript\_include\_tag</tt>
* <tt>link\_to</tt>
* <tt>url\_for</tt>
* <tt>number\_to\_currency</tt>
* <tt>image\_tag</tt>
* hay muchos, decubridlos poco a poco
* en serio: <http://apidock.com/rails/ActionView/Helpers>

!SLIDE smbullets incremental
# FormHelpers
* dos _familias_: los <tt>*\_tag</tt> y los _object_
* los _tag_: genera html normal
* los de _objeto_: optimizados para recursos Rails

!SLIDE smbullets incremental
# los forms Rails
* html normal
* convenciones de nombres
* namespace con '[' y ']'
* por ejemplo: <tt></tt>

!SLIDE code
## form\_for en acción ##
    @@@ html
    <%= form_for @article, :url => { :action => "create" },
        :html => {:class => "nifty_form"} do |f| %>
      <%= f.text_field :title %>
      <%= f.text_area :body, :size => "60x12" %>
      <%= f.submit "Create" %>
    <% end %>
    <form accept-charset="UTF-8" action="/articles/create" method="post" class="nifty_form">
      <input id="article_title" name="article[title]" size="30" type="text" />
      <textarea id="article_body" name="article[body]" cols="60" rows="12"></textarea>
      <input name="commit" type="submit" value="Create" />
    </form>

!SLIDE smbullets incremental
## hay gente que nunca tiene bastante
* Formtastic
* **SimpleForm**
* algunos integran con Bootstrap y amigos
* tutoriales en [Railscasts](http://www.railscasts.com)
* <https://www.ruby-toolbox.com/categories/rails_form_builders>
