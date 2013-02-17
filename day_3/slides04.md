<!SLIDE title-slide transition=fade>

# Przykłady zaawansowane

<!SLIDE smaller bullets incremental transition=fade>

  * Dodaj metodę przeszukującą po "inicjałach" nazwy produktu

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Wszystkie posty z polem `text`(:title, :description, 
      # lub :comments) zawierające 'ruby'
      Product.search { fulltext 'ruby' }

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Producty z ruby, wyskoczą wyżej
      # jeśli ruby pojawi się w nazwie
      
      Product.search do
        fulltext 'ruby' do
          boost_fields :title => 2.0
        end
      end

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Products z ruby, wyżej jeśli produkt promowany
      
      Product.search do
        fulltext 'ruby' do
          boost(2.0) { with(:featured, true) }
        end
      end

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Producty z ruby *tylko* w nazwie
      
      Product.search do
        fulltext 'ruby' do
          fields(:title)
        end
      end

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Produkty z ruby w tytule dopalone
      # a z ruby w opisie - nie
      
      Product.search do
        fulltext 'ruby' do
          fields(:description, :title => 2.0)
        end
      end

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Tylko dokładne dopasowania

      Product.search do
        fulltext '"ruby programming"'
      end


<!SLIDE smaller transition=fade>

    @@@ ruby
      # Product ze średnią oceną pomiędzy 3.0 a 5.0
      
      Product.search do
        with(:average_rating, 3.0..5.0)
      end

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Product z kategoriami 1, 3, lub 5
      
      Product.search do
        with(:category_ids, [1, 3, 5])
      end

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Produkty opublikowane co najmniej tydzień temu
      
      Product.search do
        with(:published_at).greater_than(1.week.ago)
      end

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Wszystko tylko nie kategorie nr 1 i 3

      Product.search do
        without(:category_ids, [1, 3])
      end


<!SLIDE smaller transition=fade>

    @@@ ruby
      # I nowość - szukanie po koordynatach

      class Product < ActiveRecord::Base
        searchable do
          # ...
          latlon(:location) { 
            Sunspot::Util::Coordinates.new(lat, lon)
          }
        end
      end

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Szukamy produktów w odległości 100 kilometrów od (32, -68)
      
      Product.search do
        with(:location).in_radius(32, -68, 100)
      end

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Szukamy w prostokącie pomiędzy
      # punktami (45, -94) a (46, -93)

      Product.search do
        with(:location).in_bounding_box([45, -94], [46, -93])
      end

<!SLIDE smaller transition=fade>

    @@@ ruby
      # Posortuj wg. odległości od punktu zero

      Product.search do
        order_by_geodist(:location, 32, -68)
      end
