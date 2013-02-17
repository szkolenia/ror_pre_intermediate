<!SLIDE title-slide transition=fade>

# Część 1 #

<!SLIDE title-slide transition=fade>

# Dlaczego HAML?

### "We have used Haml in every web project for the past 2 years and it has saved us an inestimable amount of time. Just brilliant."

<!SLIDE smaller transition=fade>

# Można tak...
    @@@ html
      <section class="container">
        <h1><%= @post.title %></h1>
        <h2><%= @post.subtitle %></h2>
        <div class="content">
          <%= @post.content %>
        </div>
      </section>

<!SLIDE smaller transition=fade>

# ale można tak!
    @@@ ruby
      %section.container
        %h1= @post.title
        %h2= @post.subtitle
        .content
          = @post.content

<!SLIDE transition=fade>

# Podstawy

<!SLIDE transition=fade>

# ERB
    @@@ html
      <strong><%= item.title %></strong>

<!SLIDE transition=fade>

# Haml
    @@@ ruby
      %strong= item.title

<!SLIDE transition=fade>

# Atrybuty

<!SLIDE smaller transition=fade>

# ERB
    @@@ html
      <strong class="code" id="message">Hello, World!</strong>

<!SLIDE smaller transition=fade>

# Haml
    @@@ ruby
      %strong{:class => "code", :id => "message"} Hello, World!
      
      %strong.code#message Hello, World!

<!SLIDE smaller transition=fade>

# ERB
    @@@ html
      <div class='content'>Hello, World!</div>

<!SLIDE smaller transition=fade>

# Haml
    @@@ ruby
      .content Hello, World!

<!SLIDE smaller transition=fade>

# ERB
    @@@ html
      <div class='item' id='item<%= item.id %>'>
        <%= item.body %>
      </div>

<!SLIDE smaller transition=fade>

# Haml
    @@@ ruby
      .item{:id => "item#{item.id}"}= item.body

<!SLIDE smaller transition=fade>

# ERB
    @@@ html
      <div id='content'>
        <div class='left column'>
          <h2>Welcome to our site!</h2>
          <p><%= print_information %></p>
        </div>
        <div class="right column">
          <%= render :partial => "sidebar" %>
        </div>
      </div>

<!SLIDE smaller transition=fade>

# Haml
    @@@ ruby
      #content
        .left.column
          %h2 Welcome to our site!
          %p= print_information
        .right.column
          = render :partial => "sidebar"

<!SLIDE smaller transition=fade>

# Można mieszać erb z html
    @@@ ruby
      %p
        <div id="blah">Blah!</div>

<!SLIDE smaller transition=fade>

# Komentowanie

<!SLIDE smaller transition=fade>

# Komentarz w hamlu    
    @@@ ruby
      %title
        = @title
        \= @title

<!SLIDE smaller transition=fade>

# Jak to wygląda w html'u
    @@@ html
      <title>
        MyPage
        = @title
      </title>

<!SLIDE smaller transition=fade>

# JavaScript też zadziała
    @@@ ruby
      %script{:type => "text/javascript",
              :src  => "javascripts/script_#{2 + 7}"}

<!SLIDE smaller transition=fade>

    @@@ html
      <script src='javascripts/script_9' type='text/javascript'>
        </script>

<!SLIDE smaller transition=fade>

# Boolean Attributes

<!SLIDE smaller transition=fade>

    @@@ html
      <input selected>

<!SLIDE smaller transition=fade>

    @@@ ruby
      %input{:selected => true}

<!SLIDE smaller transition=fade>

    @@@ ruby
      %a{:href=>"/posts", :data => {:author_id => 123}}
        Posts By Author

<!SLIDE smaller transition=fade>
  
    @@@ html
      <a data-author-id='123' href='/posts'>
        Posts By Author
      </a>

<!SLIDE smaller transition=fade>

    @@@ html
      <div id='things'>
        <span id='rice'>Chicken Fried</span>
        <p class='beans' food='true'>The magical fruit</p>
        <h1 class='class otherclass' id='id'>La La La</h1>
      </div>

<!SLIDE smaller transition=fade>

    @@@ ruby
      %div#things
        %span#rice Chicken Fried
        %p.beans{ :food => 'true' } The magical fruit
        %h1.class.otherclass#id La La La

<!SLIDE smaller transition=fade>

    @@@ html
      <div id='content'>
        <div class='articles'>
          <div class='article title'>
            Doogie Howser Comes Out
          </div>
          <div class='article date'>2006-11-05</div>
          <div class='article entry'>
            Neil Patrick Harris would like to...
          </div>
        </div>
      </div>

<!SLIDE smaller transition=fade>  

    @@@ ruby
      %div#content
        %div.articles
          %div.article.title Doogie Howser Comes Out
          %div.article.date 2006-11-05
          %div.article.entry
            Neil Patrick Harris would like to...

<!SLIDE smaller transition=fade>

# Pamiętajcie że można szybciej

    @@@ html
      <div id='collection'>
        <div class='item'>
          <div class='description'>What a cool item!</div>
        </div>
      </div>

<!SLIDE smaller transition=fade>

    @@@ ruby
      %div#collection
        %div.item
          %div.description What a cool item!

      #collection
        .item
          .description What a cool item!

<!SLIDE smaller transition=fade>

# Samozakańczające się tagi

<!SLIDE smaller transition=fade>

    @@@ ruby
      %br/
      %meta{
          'http-equiv' => 'Content-Type', 
        :content => 'text/html'}/

<!SLIDE smaller transition=fade>

    @@@ html
      <br />
      <meta 
        http-equiv='Content-Type'
        content='text/html'
      />

<!SLIDE smaller transition=fade>

# Komentarze na wiele linii
    @@@ ruby
      /
        %p To się nie wyświetli...
        %div
          %h1 Ponieważ jest zakomentowane!

<!SLIDE smaller transition=fade>

    @@@ html
      <!--
        <p>To się nie wyświetli...</p>
        <div>
          <h1>Ponieważ jest zakomentowane!</h1>
        </div>
      -->

<!SLIDE smaller transition=fade>

    @@@ ruby
      %p foo
      -# This is a comment
      %p bar
      is compiled to:

<!SLIDE smaller transition=fade>

    @@@ html
      <p>foo</p>
      <p>bar</p>

<!SLIDE smaller transition=fade>

    @@@ ruby
      - foo = "hello"
      - foo << " there"
      - foo << " you!"
      %p= foo

<!SLIDE smaller transition=fade>

    @@@ html
      <p>
        hello there you!
      </p>

<!SLIDE smaller transition=fade>

# Filtry
    @@@ ruby
      :cdata
      :coffee
      :css
      :erb
      :escaped
      :javascript
      :less
      :markdown
      :maruku
      :plain
      :preserve
      :ruby
      :sass
      :scss
      :textile

<!SLIDE smaller transition=fade>    

# Interpolacja działa również z filtrami

<!SLIDE smaller transition=fade>    

    @@@ ruby
      :javascript
        $(document).ready(function() {
          alert(#{@message.to_json});
        });

<!SLIDE smaller transition=fade>

    @@@ html
      <script type='text/javascript'>
        //<![CDATA[
          $(document).ready(function() {
            alert("Hi there!");
          });
        //]]>
      </script>

<!SLIDE commandline incremental transition=fade>

# Powinniście to zobaczyć na początku ;)

    $ html2haml plik.html.erb plik.html.haml