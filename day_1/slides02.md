# Część 2

## Wracamy do księgarni

<!SLIDE smaller bullets incremental transition=fade>

# Zaczynamy pracę

  * Forkujemy projekt https://github.com/szkolenia/depot.git
  * Klonujemy swoją kopię projektu

# Zadanie #1
  
  * Tytuł produktu musi mieć minimum 10 znaków
  * Test?
  * Commit

<!SLIDE smaller transition=fade>

# Zadanie #2

  * Dodaj wyświetlanie daty i godziny na sidebarze
  * Napisz test funkcjonalny dowolnego kontrolera używając matchera assert_select


<!SLIDE smaller transition=fade>

# Zadanie #3

  * Utwórz w sesji nową zmienną, w której przechowasz liczbę odwiedzin użytkownika na akcji index w store_controller
  * Wyświetl licznik u góry listy produktów, (helper pluralize) może być pomocny
  * Wyczyść licznik za każdym razem, gdy użytkownik doda produkt do koszyka
  * Wyświetlaj licznik, tylko gdy jego wartość > 5

<!SLIDE smaller transition=fade>

# Zadanie #4
  
  .notes Utwórz migrację, która skopiuje cenę produktu do elementu w koszyku (line item) oraz zmień metodę _add_product()_ w modelu _Cart_, by zapisywała cenę produktu, za każdym razem, gdy jest dodawany do koszyka


  * Dodaj metodę, która pozwoli na usuwanie pojedynczych elementów z koszyka. Każdy wiersz będzie wymagał zrobienia przycisku, wywołującego akcję w _LineItemsController_

<!SLIDE smaller transition=fade>

# Zadanie #5

  * Dodaj przycisk, który pozwoli zmniejszyć liczbę produktów w koszyku lub usunie całkowicie produkt, gdy ilość wyniesie 0

<!SLIDE smaller transition=fade>

# Zadanie #6

  * Dodaj widoki w formacie HTML, XML i JSON dla requestu _who___bought_
  * Eksperymentuj z formatem XML renderując _@product.to_xml(include::orders)_, zrób to samo dla JSON

<!SLIDE smaller transition=fade>

# Zadanie #7
  * Zablokuj możliwość kliknięcia przycisku _Checkout_, gdy znajdujesz się już na tym widoku

<!SLIDE smaller transition=fade>

# Zadanie #8
  * Dodaj kolumnę _ship___date_ do tabeli _orders_ i wyślij klientowi e-mail, gdy wartość zostanie zaktualizowana przez _OrdersController_
  * Wyślij email do administratora, gdy występuje wyjątek w aplikacji
  * Napisz testy integracyjne do obu przypadków



