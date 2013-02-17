<!SLIDE title-slide transition=fade>

# Własna wyszukiwarka pełnotekstowa

<!SLIDE small transition=fade>

# Pomysły?
    @@@ sql
      SELECT * FROM Product WHERE title LIKE 's%'

<!SLIDE commandline incremental transition=fade>

# Sprawdźmy to..
    
    $ rails g scaffold Product ...
    $ rake db:migrate
    $ rake db:fixtures:load
    $ rais c

<!--<!SLIDE small transition=fade>                  -->

<!--    @@@ ruby                                    -->
<!--      # Gemfile                                 -->

<!--      gem "twitter-bootstrap-rails"             -->

<!--<!SLIDE commandline incremental transition=fade>-->
    
<!--    $ rails g bootstrap:layout application fixed-->

<!SLIDE smaller transition=fade>

# A może w kontrollerze?

    @@@ruby
      @products = Product.where('title LIKE ?', params[:title])

<!SLIDE small bullets incremental transition=fade>

# Czy aby na pewno?

  * co jak mielibyśmy pominąć znaczenie wielkości znaków (i pominąć polskie znaki)?
  * co jak byśmy chcieli SZYBKO przeszukać wszystkie recenzje do danego produktu?
  * zapytań jest tak dużo że aplikacja zwalnia?

<!SLIDE transition=fade>

# Na szczęście jest [SOLR] (https://www.youtube.com/watch?v=xhyBHmBrAMc)

<!SLIDE transition=fade>

# Instalacja __Sunspot__'a

    @@@ ruby
      # Gemfile
      source 'http://rubygems.org'

      gem 'rails'
      gem 'sunspot_rails'

<!SLIDE commandline incremental transition=fade>

    $ rails g sunspot_rails:install
    # nowy plik konfiguracyjny /config/sunspot.yml
    
    $ rake sunspot:solr:start
    # Tutaj przyda się Java

<!SLIDE transition=fade>

# Jak użyć Sunspota?

<!SLIDE commandline incremental transition=fade>

    $ bundle exec rake db:fixtures:load

<!SLIDE smaller transition=fade>

## Ustawmy w modelu które pola mają być przeszukiwalne

    @@@ ruby
      # app/models/product.rb
    
      class Product < ActiveRecord::Base
        attr_accessible :title, :description, :published_at
        
        searchable do
          text :title, :description
        end
      end

<!SLIDE smaller incremental transition=fade>


    $ bundle exec rails g migration AddPublishedAtToProduct \
      published_at:datetime

</pre>

    @@@ ruby
    class AddPublishedAtToProduct < ActiveRecord::Migration
      def change
        add_column :products, :published_at, :datetime
      end
    end


    class Product < ActiveRecord::Base
      scope :published, lambda { where('published_at <= ?', Time.zone.now) }
    end

    class StoreController < ActiveRecordBase
      def index
        @products = Product.published.order(:title)
        # ...
      end
    end

<!SLIDE smaller incremental transition=fade>

    

<!SLIDE commandline incremental transition=fade>

### Sunspot automatycznie indeksuje wszystkie nowe obiekty, ale aby zaindeksować starsze:

    $ bundle exec rake sunspot:reindex

<!SLIDE smaller transition=fade>

# Dodajmy pole przeszukiwania

    @@@ html
      <!-- app/views/store/index.html.erb -->

      <%= form_tag search_path, :method => :get do %>
        <p>
          <%= text_field_tag :search, params[:search] %>
          <%= submit_tag "Search", :name => nil %>
        </p>
      <% end %>

<!SLIDE small transition=fade>

# Poprawmy kontroller

    @@@ ruby
      # app/controllers/store_controller.rb
      
      def search
        @search = Product.search do
          fulltext params[:search]
        end
        @products = @search.results
      end

<!SLIDE transition=fade>

# Sprawdźmy to - co zostanie przeszukane?

<!SLIDE transition=fade>

## Wynik zapytania zwraca listę produktów gdzie zapytanie zawiera się zarówno w nazwie jak i w opisie

<!SLIDE small transition=fade>

## Nazwa powinna być ważniejsza od opisu!

    @@@ ruby
      # app/models/product.rb

      class Product < ActiveRecord::Base
        attr_accessible :title, :description, :published_at
        
        searchable do
      ➤   text :title, :boost => 5
          text :description
        end
      end

<!SLIDE transition=fade>

## Nie musimy szukać tylko po kolumnach z bazy danych.. __możemy przeszukać dowolną metodę!__

<!SLIDE smaller transition=fade>

    @@@ ruby
      # app/models/product.rb
      
      class Product < ActiveRecord::Base
        attr_accessible :title, :description, :published_at
        
        searchable do
          text :name, :boost => 5
      ➤    text :description, :publish_month
        end
        
      ➤  def publish_month
      ➤    published_at.strftime("%B %Y")
      ➤  end
        
      end

<!SLIDE transition=fade>

# Działa?

<!SLIDE transition=fade>

# Pamiętaj o reindeksacji...

<!SLIDE commandline incremental transition=fade>

### Dodajmy blok przeszukujący zawartość wszystkich komentarzy/recenzji do tego produktu

    $ rails g scaffold Comment content:string product_id:integer
    $ rake db:fixtures:load

<!SLIDE smaller transition=fade>

    @@@ ruby
      # app/models/product.rb
      
      class Product < ActiveRecord::Base
        attr_accessible :title, :description, :published_at
        has_many :comments
        
        searchable do
          text :title, :boost => 5
          text :description, :publish_month
          text :comments do
            # to są komentarze do danego produktu
        ➤  comments.map(&:content)
          end
        end
        
        def publish_month
          published_at.strftime("%B %Y")
        end
        
      end

<!SLIDE smaller transition=fade>

# Przeszukajmy coś więcej niż tekst.. może czas?

    @@@ ruby 
      # app/models/product.rb
      
      class Product < ActiveRecord::Base
        attr_accessible :title, :description, :published_at
        has_many :comments
        
        searchable do
          text :title, :boost => 5
          text :description, :publish_month
          text :comments do
            comments.map(&:content)
          end
      ➤  time :published_at
        end
        
        def publish_month
          published_at.strftime("%B %Y")
        end
        
      end

<!SLIDE smaller transition=fade>

# Przeszukajmy tylko te produkty które są na pewno w sklepie
  
    @@@ ruby
      # app/controllers/products_controller.rb

      def index
        @search = Product.search do
          fulltext params[:search]
      ➤   with(:published_at).less_than(Time.now)
        end
        @products = @search.results
      end

<!SLIDE smaller transition=fade>

# Podzielmy to
## Dodajmy atrybut "string" do bloku przeszukiwania dla metody publish_month

    @@@ ruby
      # app/models/product.rb

      class Product < ActiveRecord::Base
        attr_accessible :title, :description, :published_at
        has_many :comments
        
        searchable do
          text :title, :boost => 5
          text :description, :publish_month
          text :comments do
            comments.map(&:content)
          end
          time :published_at
      ➤  string :publish_month
        end
        
        def publish_month
          published_at.strftime("%B %Y")
        end
      end

<!SLIDE small transition=fade>

# Teraz możemy podzielić wyniki wyzukiwania na podgrupy.

    @@@ ruby
      # app/controllers/products_controller.rb
      
      def index
        @search = product.search do
          fulltext params[:search]
          with(:published_at).less_than(Time.zone.now)
      ➤  facet(:publish_month)
        end
        @products = @search.results
      end

<!SLIDE smaller transition=fade>

# Wyświelmy podgupy na widoku.
    
    @@@ html
      <!-- app/views/products/index.html.erb -->
      
      <div id="facets">
        <h3>Published</h3>
        <ul>
          <% for row in @search.facet(:publish_month).rows %>
            <li>
              <% if params[:month].blank? %>
                <%= link_to row.value, :month => row.value %>
                  (<%= row.count %>)
      
<!SLIDE smaller transition=fade>

# to niestety nie wszystko, musimy teraz odfiltrować nasz nowy parametr

    @@@ ruby
      # app/controllers/products_controller.rb
      
      def index
        @search = product.search do
          fulltext params[:search]
          with(:published_at).less_than(Time.zone.now)
          facet(:publish_month)
          if params[:month].present?
            with(:publish_month, params[:month])
          end
        end
        @products = @search.results
      end
