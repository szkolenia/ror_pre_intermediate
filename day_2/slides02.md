<!SLIDE title-slide transition=fade>

# Część 2


<!SLIDE smaller bullets incremental transition=fade>

# Capistrano

    @@@ shell
    # Gemfile
    gem 'capistrano'
</pre>

    $ bundle 
    
    $ bundle pack

    $ git add .

    $ git commit -m "Capistrano & bundled gems"

    $ capify .

<!SLIDE smaller bullets incremental transition=fade>

    @@@ ruby
    # ./Capfile

    load 'deploy'
    # Uncomment if you are using Rails' asset pipeline
    load 'deploy/assets'
    Dir['vendor/gems/*/recipes/*.rb',
      'vendor/plugins/*/recipes/*.rb'].
      each { |plugin| load(plugin) }
    load 'config/deploy' # remove this line to skip loading 
    any of the default tasks

<!SLIDE smaller bullets incremental transition=fade>

    @@@ ruby
    # config/deploy.rb
    require 'bundler/capistrano'
    set :user, 'szkolenie'
    set :domain, 'depot.szkolenie.com'
    set :application, 'depot'
    
    # adjust if you are using RVM, remove if you are not
    require "rvm/capistrano"
    set :rvm_ruby_string, '1.9.3'
    set :rvm_type, :user
    
    # file paths
    set :repository,  "git@github.com:nazwa_uzytkownika/depot.git" 
    set :deploy_to, "/home/#{user}/#{domain}" 
    
    # distribute your applications across servers (the 
    # instructions below put them
    # all on the same server, defined above as 
    #'domain', adjust as necessary)
    role :app, domain
    role :web, domain
    role :db, domain, :primary => true
    # ...

<!SLIDE smaller bullets incremental transition=fade>

    @@@ ruby
    # config/deploy.rb
    # you might need to set this if you aren't seeing password prompts
    # default_run_options[:pty] = true
    
    set :deploy_via, :remote_cache
    set :scm, 'git'
    set :branch, 'master'
    set :scm_verbose, true
    set :use_sudo, false
    set :rails_env, :production
    
    namespace :deploy do
      desc "cause Passenger to initiate a restart"
      task :restart do
        run "touch #{current_path}/tmp/restart.txt" 
      end
      desc "reload the database with seed data"
      task :seed do
        run "cd #{current_path}; \
          rake db:seed RAILS_ENV=#{rails_env}"
      end
    end


<!SLIDE smaller bullets incremental transition=fade>

## Konfiguracja vhosta

    @@@ apache
    # /etc/apache2/sites-available/depot.szkolenie.com
    <VirtualHost *:80>
      ServerName depot.szkolenie.com
      DocumentRoot /home/szkolenie/depot.szkolenie.com/current/public/
      <Directory /home/szkolenie/depot.szkolenie.com/current/public>
        AllowOverride all
        Options -MultiViews
        Order allow,deny
        Allow from all
      </Directory>
    </VirtualHost>
</pre>

## Forwardujemy klucz publiczny

    @@@ shell
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
    ssh-add ~/.ssh/id_rsa.pub

## Forwardujemy klucz publiczny

    @@@ shell
    # ~/.ssh/config
    Host *
      ForwardAgent yes

<!SLIDE smaller bullets incremental transition=fade>

    $ cap deploy:setup

    $ cap deploy:check

    $ cap deploy:migrate

    $ cap deploy:seed

    $ git add .
    
    $ git commit -m "add cap files"

    $ git push

    $ cap deploy

    $ cap deploy:rollback
