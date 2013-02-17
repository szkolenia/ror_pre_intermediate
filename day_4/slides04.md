<!SLIDE title-slide transition=fade>

# A gdzie Wasze repo?

<!SLIDE smaller bullets incremental transition=fade>

# Plan na dziś #
  
  * Prawa userów i adminów
  * Poczekalnia obrazków
  * Kategorie dla obrazków
  * Ładne URL'e
  * Komentarze z FB
  * Logowanie przez zewnętrzną aplikację

<!SLIDE transition=fade>

# Prawa i obowiązki userów i adminów
## [Cancan od Rayana] (https://github.com/ryanb/cancan)

<!SLIDE smaller transition=fade>

# Poczekalnia obrazków
## Małe poprawki w modelu i dodanie [scope] (http://guides.rubyonrails.org/active_record_querying.html#scopes)'a
    
    @@@ ruby
    scope :published,   where(published: true)
    scope :unpublished, where(published: false)

<!SLIDE commandline incremental transition=fade>

    $ rails g controller WaitingPhotos index

<!SLIDE transition=fade>

# Maile dla leniwych adminów
## Czyli [Emacsem przez sendmail] (http://www.youtube.com/watch?v=wFXLzr86MQ4)

<!SLIDE transition=fade>

# Kategorie dla obrazków
## Podzielmy na kategorie, dodajmy TOP wyświetleń

<!SLIDE smaller transition=fade>

    @@@ ruby
      def self.up
        create_table 'categories_photos', :id => false do |t|
          t.integer :category_id, :null => false
          t.integer :photo_id,    :null => false
        end
      end

<!SLIDE smaller transition=fade>

    @@@ ruby
      # W kontrolerze należy uzupełnić zmienną @categories
      # Formularz edycji zdjęcia
      = form_for(@photo) do |f|
        # .....
        - for category in @categories
          = check_box_tag "photo[category_ids][]",
            "#{category.id}", 
            @photo.categories.include?(category)
          = category.name

<!SLIDE transition=fade>

#Ładne URL'e
## bez [friendly_id] (https://github.com/norman/friendly_id) ani rusz.

<!SLIDE smaller transition=fade>

# Komentarze z FB 

    @@@ ruby
      #fb-root
        %script{ :src => 
          "https://connect.facebook.net/pl_PL/all.js#xfbml=1"
        }
        %fb:comments{ :href => 
          "#{ActiveSupport::Inflector.transliterate(
              CGI::unescape(request.url)
            ).gsub('http://www.', 'http://').gsub(
              /\?.+\z/, ''
            )}",
          :width =>'600' }
