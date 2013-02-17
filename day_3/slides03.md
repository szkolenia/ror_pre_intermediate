<!SLIDE title-slide transition=fade>

# Część 3 #

<!SLIDE transition=fade>

# Autopodpowiadanie

<!SLIDE transition=fade>

    @@@ ruby
      # Gemfile

      gem 'rails3-jquery-autocomplete'

<!SLIDE transition=fade>

# Aby pozbyć się polskich znaków

<!SLIDE smaller transition=fade>

# Poprawmy w modelu indeksowanie

    @@@ ruby
      ActiveSupport::Inflector.transliterate('Łódź')
      # => 'Lodz'.downcase
      # => 'lodz'

<!SLIDE transition=fade>

# Aby pozbyć się layoutu i odpowiedzieć czysym json'em

<!SLIDE transition=fade>

    @@@ ruby
      layout nil
      respond_to :json

<!SLIDE smaller transition=fade>

    @@@ ruby
      # app/controllers/search_controller.rb
      class SearchController < ApplicationController
        layout nil
        respond_to :json

        def index
          term       = ActiveSupport::Inflector.transliterate(
              params[:term]
            ).downcase
          words      = term.split(/\s+/)
          prefix     = words.pop
          full_words = words.join(' ')
          all_fields = [:name, :description, :...]

          list = Sunspot.search(Product) do
            keywords(full_words, :fields => all_fields)
            text_fields do |text_fields_query|
              text_fields_query.any_of do |any_of_query|
                all_fields.each do |text_field|
                  any_of_query.with(text_field).starting_with(
                    prefix.downcase
                  )
                end
              end
            end
          end

<!SLIDE smaller transition=fade>

    @@@ ruby
      (...)

          @data = []

          # Zbudujmy hash który zrozumie jQuery
          
          list.each_hit_with_result do |hit, result|
            @data << {
              :label       => result.name,
              :id          => result.id,
              :value       => result.name,
              :description => "#{result.name}<br>#{result.description}",
              :link        => products_path(result)
            }
          end

          respond_with(@data.uniq.to_json)
        end
      end

<!SLIDE smaller transition=fade>

# i w widoku
## = text\_field\_tag :term, params[:term], 
## :'data-autocomplete' => search_path(:json)

<!SLIDE smaller transition=fade>

# Teraz pozostaje to tylko odpowiednio 
# ostylować i dodać akcję w JSie żeby
# kliknięcie przeniosło nas na odpowiedni URL
