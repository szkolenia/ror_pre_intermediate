<!SLIDE title-slide transition=fade>

# Upload plików

<!SLIDE smaller transition=fade>

    @@@ ruby
    class CreatePictures < ActiveRecord::Migration
      def change
        create_table :pictures do |t|
          t.string :comment
          t.string :name
          t.string :content_type
          t.binary :data, :limit => 1.megabyte # default 64kB
        end
      end
    end


    # app/controllers/upload_controller.rb
    class UploadController < ApplicationController
      def get
        @picture = Picture.new
      end
      # . . .
    end

<!SLIDE smaller transition=fade>

    @@@ html
    <!-- app/views/upload/get.html.erb -->
    <% form_for(:picture, 
                url: {action: 'save'}, 
                html: {multipart: true}) do |form| %>
    
        Comment:             <%= form.text_field("comment") %>
        <br/>
        Upload your picture:
        <%= form.file_field("uploaded_picture") %><br/>
    
        <%= submit_tag("Upload file") %>
    <% end %>


<!SLIDE smaller transition=fade>

    @@@ ruby
    # app/models/picture.rb
    class Picture < ActiveRecord::Base
    
      validates_format_of :content_type, 
                          with: /^image/,
                          message: "--- you can only \
      upload pictures"
    
      def uploaded_picture=(picture_field)
        self.name = base_part_of(picture_field.original_filename)
        self.content_type = picture_field.content_type.chomp
        self.data         = picture_field.read
      end
    
      def base_part_of(file_name)
        File.basename(file_name).gsub(/[^\w._-]/, '')
      end
    end

<!SLIDE smaller transition=fade>


    @@@ruby
    class UploadController < ApplicationController 
      def save
        @picture = Picture.new(params[:picture])
        if @picture.save
          redirect_to(action: 'show', id: @picture.id)
        else
          render(action: :get)
        end
      end

      def show
        @picture = Picture.find(params[:id])
      end

      def picture
        @picture = Picture.find(params[:id])
        send_data(@picture.data,
                  filename: @picture.name,
                  type: @picture.content_type,
                  disposition: "inline")
      end
    end


<!SLIDE smaller transition=fade>

    @@@ html
    <!-- app/views/picture/show.html.erb -->
    <h3><%= @picture.comment %></h3>
    
    <img src="<%= url_for(:action => 'picture',
      :id => @picture.id) %>"/>
