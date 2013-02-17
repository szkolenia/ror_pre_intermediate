<!SLIDE title-slide transition=fade>

# Część pierwsza

## Deploy z MySQL i Passenger

<!SLIDE smaller bullets incremental transition=fade>

    $ sudo apt-get install apache2


    $ gem install passenger


    $ passenger-install-apache2-module

    
    $ sudo apt-get install libcurl4-openssl-dev \
      apache2-prefork-dev libapr1-dev libaprutil1-dev


    $ apachectl -V | grep HTTPD_ROOT
    $ apachectl -V | grep SERVER_CONFIG_FILE
</pre>

<!SLIDE smaller bullets incremental transition=fade>

    @@@ apache
    # /etc/apache2/modules-available/passenger.load
    LoadModule passenger_module ...
</pre>


    @@@ apache
    # /etc/apache2/modules-available/passenger.conf
    PassengerRoot /home/...
    PassengerRuny /home/...
</pre>

    $ sudo a2enmod passenger

</pre>

    @@@ apache
    # /etc/apache2/ports.conf
    NameVirtualHost *:80 
    # ...
    Listen 80
</pre>


<!SLIDE smaller bullets incremental transition=fade>

## Konfiguracja vhosta

    @@@ apache
    # /etc/apache2/sites-available/depot.szkolenie.com
    <VirtualHost *:80>
      ServerName depot.szkolenie.com
      DocumentRoot /home/szkolenie/depot/public/
      <Directory /home/szkolenie/depot/public>
        AllowOverride all
        Options -MultiViews
        Order allow,deny
        Allow from all
      </Directory>
    </VirtualHost>
</pre>

## Restart Apache

    $ sudo apachectl restart
</pre>

## Edytujemy hosts

    @@@ shell
    # /etc/hosts
    127.0.0.1 depot.szkolenie.com

    $ RAILS_ENV=production rake db:setup
