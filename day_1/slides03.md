<!SLIDE title-slide transition=fade>

# Część 3
  
## Użytkownicy i autentykacja

<!SLIDE smaller bullets incremental transition=fade>

# Tworzymy użytkownika

    $ rails generate scaffold User name:string \
      password_digest:string

    $ rake db:migrate

## Model
  
    @@@ ruby
      class User < ActiveRecord::Base
        attr_accessible :name, :password, :password_confirmation
        validates :name, presence: true, uniqueness: true

        has_secure_password
      end

## Gemfile
    
    @@@ ruby
      gem 'bcrypt-ruby', '~> 3.0.0'

    $ bundle install

<!SLIDE smaller bullets incremental transition=fade>

# Modyfikujemy kontroler
  * Nie potrzebujemy akcji show, więc przekierujmy od razu na index
  * Wyświetlmy notice z nazwą użytkownika


<!SLIDE smaller bullets incremental transition=fade>

    @@@ ruby
    # app/controllers/users_controller.rb

    def create 
      @user = User.new(params[:user])
      respond_to do |format|
        if @user.save
          ➤	format.html { redirect_to users_url,
          ➤	  notice: "User #{@user.name} \
                was successfully created." }
          format.json { render json: @user, 
            status: :created, location: @user }
        else
          format.html { render action: "new" }
          format.json { render json: @user.errors,
            status: :unprocessable_entity }
        end
      end
    end

<!SLIDE smaller bullets incremental transition=fade>

    @@@ ruby
    # app/controllers/users_controller.rb

    def update
      @user = User.find(params[:id])
      respond_to do |format|
        if @user.update_attributes(params[:user])
          ➤	format.html { redirect_to users_url,
            notice: "User #{@user.name} \
              was successfully updated." }
          ➤	format.json { head :no_content }
        else
          format.html { render action: "edit" }
          format.json { render json: @user.errors,
            status: :unprocessable_entity }
        end
      end
    end

<!SLIDE smaller bullets incremental transition=fade>

## Wyświetlmy użytkowników alfabetycznie

    @@@ ruby
    def index
    ➤ @users = User.order(:name)
      respond_to do |format|
        format.html # index.html.erb
        format.json { render json: @users }
      end
    end

<!SLIDE smaller bullets incremental transition=fade>

    
    @@@ html
    <!-- app/views/users/index.html.erb -->
    <h1>Listing users</h1>
      ➤<% if notice %>
      ➤  <p id="notice"><%= notice %></p>
      ➤<% end %>
      <table>
        <tr>
          <th>Name</th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
        <% @users.each do |user| %>
          <tr>
            <td><%= user.name %></td>
            <td><%= link_to 'Show', user %></td>
            <td><%= link_to 'Edit', edit_user_path(user) %></td>
            <td><%= link_to 'Destroy', user, method: :delete, \
              data: { confirm: 'Are you sure?' } %></td>
          </tr>
        <% end %>
      </table>
      <br />
      <%= link_to 'New User', new_user_path %>

<!SLIDE smaller bullets incremental transition=fade>

## Modyfikujemy formularz

    
    @@@ html
      <!-- app/views/users/_form.html.erb --> 
      <div class="depot_form">
      <%= form_for @user do |f| %>
        # ...
        <fieldset>
        <legend>Enter User Details</legend>
        <div>
          <%= f.label :name %>:
          <%= f.text_field :name, size: 40 %>
        </div>
        <div>
          <%= f.label :password, 'Password' %>:
          <%= f.password_field :password, size: 40 %>
        </div> 
        <div>
          <%= f.label :password_confirmation, 'Confirm' %>:
          <%= f.password_field :password_confirmation, size: 40 %>
        </div>
        <div>
          <%= f.submit %>
        </div>
        </fieldset>
      <% end %>
      </div>

<!SLIDE smaller bullets incremental transition=fade>

# Sprawdźmy

* _http://localhost:3000/users/new_
* Create user
* $ mysql -u root -p
* select * from users;

<!SLIDE smaller bullets incremental transition=fade>

# Testy wymagają aktualizacji

      @@@ ruby
      # test/functional/users_controller_test.rb
      class UsersControllerTest < ActionController::TestCase
        ➤setup do
        ➤  @input_attributes = {
        ➤    name:                  "sam",
        ➤    password:              "private",
        ➤    password_confirmation: "private"
        ➤  }

          @user = users(:one)
        end
        # ...
        test "should create user" do
          assert_difference('User.count') do
         ➤    post :create, user: @input_attributes
          end
         ➤  assert_redirected_to users_path
        end
        # ...
        test "should update user" do
        ➤	put :update, id: @user, user: @input_attributes
        ➤	assert_redirected_to users_path
        end
      end
      
      
<!SLIDE smaller bullets incremental transition=fade>

# Autentykacja


* Formularz do podania loginu i hasła
* Odnotować fakt, że użytkownik jest zalogowany
* Ograniczyć dostęp do części administracyjnych w aplikacji


.notes Potrzebujemy sessions_controller

<!SLIDE smaller bullets incremental transition=fade>

    @@@ shell
    $ rails generate controller Sessions new create destroy 
    
    $ rails generate controller Admin index


<!SLIDE smaller bullets incremental transition=fade>

    @@@ruby
    # app/controllers/sessions_controller.rb

    ➤def create
    ➤  user = User.find_by_name(params[:name])
    ➤  if user and user.authenticate(params[:password])
    ➤    session[:user_id] = user.id
    ➤    redirect_to admin_url
    ➤  else
    ➤    redirect_to login_url,
           alert: "Invalid user/password combination"
    ➤  end
    ➤end

<!SLIDE smaller bullets incremental transition=fade>

## Tworzymy formularz, który nie jest związany z modelem


    @@@ html
    <!-- app/views/sessions/new.html.erb -->
    <div class="depot_form">
    <% if flash[:alert] %>
      <p id="notice"><%= flash[:alert] %></p>
    <% end %>
    <%= form_tag do %>
      <fieldset>
        <legend>Please Log In</legend>
        <div>
          <%= label_tag :name, 'Name:' %>
          <%= text_field_tag :name, params[:name] %>
        </div>
        <div>
          <%= label_tag :password, 'Password:' %>
          <%= password_field_tag :password, params[:password] %>
        </div>
        <div>
          <%= submit_tag "Login" %>
        </div>
      </fieldset>
    <% end %>
    </div>


.notes Czym ten formularz się różni (chodzi o form_tag, *_tag)


<!SLIDE smaller bullets incremental transition=fade>

    @@@ ruby
    # app/controllers/sessions_controller.rb
    def destroy
     ➤	session[:user_id] = nil
     ➤	redirect_to store_url, notice: "Logged out"
    end

<!SLIDE smaller bullets incremental transition=fade>

    @@@ html
    <!-- app/views/admin/index.html.erb -->
    <h1>Welcome</h1>

    It's <%= Time.now %>
    We have <%= pluralize(@total_orders, "order") %>.


<!SLIDE smaller bullets incremental transition=fade>

    @@@ruby
    class AdminController < ApplicationController 
      def index
      ➤	@total_orders = Order.count
      end
    end
    

<!SLIDE smaller bullets incremental transition=fade>

## Nie użyliśmy scaffoldu

## ręcznie musimy określić, które ścieżki mają odpowiadać na akcje inne,

## niż GET

    @@@ ruby
    Depot::Application.routes.draw do
      get 'admin' => 'admin#index'
    
      controller :sessions do
        get  'login' => :new
        post 'login' => :create
        delete 'logout' => :destroy
      end
    
      resources :users
    
      resources :orders
    
      # ...
    end

<!SLIDE smaller bullets incremental transition=fade>

    @@@ ruby
    # test/functional/sessions_controller_test.rb
    require 'test_helper'
    
    class SessionsControllerTest < ActionController::TestCase
      test "should get new" do
        get :new
        assert_response :success
      end
    
      test "should login" do
        dave = users(:one)
        post :create, name: dave.name, password: 'secret'
        assert_redirected_to admin_url
        assert_equal dave.id, session[:user_id]
      end
    
      test "should fail login" do
        dave = users(:one)
        post :create, name: dave.name, password: 'wrong'
        assert_redirected_to login_url
      end
    
      test "should logout" do
        delete :destroy
        assert_redirected_to store_url
      end
    end

<!SLIDE smaller bullets incremental transition=fade>

    @@@ruby
    # test/fixtures/users.yml
    one:
    name: dave
    password_digest: <%= BCrypt::Password.create('secret') %>
    two:
    name: MyString
    password_digest: MyString


<!SLIDE smaller bullets incremental transition=fade>

# Ograniczanie dostępu

* Chcemy zabronić dostępu do określonych akcji/widoków
* Skorzystamy z filtrów, wchodzących w skład _Rails_
* Pozwalają one przechwycić wywołanie metody i wykonać zdefiniowaną akcję przed lub po metodzie
* W naszym przypadku użyjemy _before___filter_ i sprawdzimy czy session[:user_id] odpowiada rekordowi w bazie
* Jeśli rekord będzie niepoprawny, obsłużymy wyjątek


<!SLIDE smaller bullets incremental transition=fade>


    @@@ ruby
    class ApplicationController < ActionController::Base
      before_filter :authorize
    
        # ...
    
      protected
    
        def authorize
          unless User.find_by_id(session[:user_id])
            redirect_to login_url, notice: "Please log in"
          end
        end
    end
</pre>

  * Callback _before___filter_ wywoła metodę _authorization_ przed wykonaniem każdej akcji z kontrolera w naszej aplikacji

<!SLIDE smaller bullets incremental transition=fade>

* Właśnie zabraliśmy sobie dostęp do aplikacji :)


* Blacklista vs. Whitelista


<!SLIDE smaller bullets incremental transition=fade>

    @@@ ruby
    class StoreController < ApplicationController
      skip_before_filter :authorize
      # ...
    end

    class SessionsController < ApplicationController
      skip_before_filter :authorize
      # ...
    end

    class CartsController < ApplicationController
      skip_before_filter :authorize,
        only: [:create, :update, :destroy]
      # ...
    end

    class LineItemsController < ApplicationController
      skip_before_filter :authorize, only: :create
      #...
    end
    
    class OrdersController < ApplicationController
      skip_before_filter :authorize, only: [:new, :create]
      # ...
    end

<!SLIDE center smaller incremental bullets>

# Czy testy będą działać?

![how about no](../gfx/how-about-no-bear.jpg)

<!SLIDE smaller bullets incremental transition=fade>

# Dodajmy metody, które pomogą przy testowaniu


    @@@ ruby
    <!-- test/test_helper.rb -->
    class ActiveSupport::TestCase
      fixtures :all
      # ...
      # Add more helper methods to be used by all tests here...
      def login_as(user)
        session[:user_id] = users(user).id
      end

      def logout
        session.delete :user_id
      end

      def setup
        login_as :one if defined? session
      end
    end
</pre>



.notes Note that the method will call only if is defined. This prevents the login from being executed in tests that do not involve a controller.

<!SLIDE smaller bullets incremental transition=fade>

      @@@ html
      <!-- app/views/layouts/application.html.erb -->
      <ul>
        <li><a href="http://www....">Home</a></li>
        <li><a href="http://www..../faq">Questions</a></li>
        <li><a href="http://www..../news">News</a></li>
        <li><a href="http://www..../contact">Contact</a></li>
      </ul>

      <% if session[:user_id] %>
        <ul>
          <li><%= link_to 'Orders',   orders_path   %></li>
          <li><%= link_to 'Products', products_path %></li>
          <li><%= link_to 'Users',    users_path    %></li>
        </ul>
        <%= button_to 'Logout', logout_path, method: :delete   %>
      <% end %>

<!SLIDE smaller bullets incremental transition=fade>

# Nie pozwólmy na usunięcie ostatniego administratora

    @@@ ruby
    # app/models/user.rb
    after_destroy :ensure_an_admin_remains

    private
      def ensure_an_admin_remains
        if User.count.zero?
          raise "Can't delete last user"
        end
      end     

    # app/controllers/users_controller.rb
    def destroy
      @user = User.find(params[:id])
      begin
        @user.destroy
        flash[:notice] = "User #{@user.name} deleted"
      rescue Exception => e
        flash[:notice] = e.message
      end
      # ...
    end

<!SLIDE center>

# Nie piszecie testów?
![disguisted](../gfx/disguisted.gif)
