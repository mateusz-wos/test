# Wstęp

Niniejszy dokument opisuje interfejs systemu płatności oferowanego przez serwis dotpay i przeznaczony jest zarówno dla podmiotów 
zainteresowanych ofertą serwisu, jak i osób zajmujących się wdrożeniem systemu.

# Charakterystyka i adres interfejsu

API panelu administracyjnego Sprzedawcy został wykonany w stylu architektury REST

Adresy pod którymi dostępny jest interfejs to:

Środowisko produkcyjne:

`https://ssl.dotpay.pl/s2/login/api/`

Środowisko testowe:

`https://ssl.dotpay.pl/test_seller/api/`


# Uwierzytelnianie i autoryzacja
> Przykładowo dla zasobu: 

> https://ssl.dotpay.pl/test_seller/api/v1/accounts/

>po wykonaniu żądania z błędnym loginem / hasłem:

```php
//nagłówki żądania

GET /test_seller/api/v1/accounts/ HTTP/1.1
Host: ssl.dotpay.pl
Accept: application/json
Content-Type: application/json
Authorization: Basic VXNlcjU4NDc6QVFNQWJxZEF2Yg==

```
> zostanie zwrócona odpowiedź:

```php
// nagłówki odpowiedzi: 

HTTP/1.1 401 Unauthorized
Content-Type: application/json
WWW-Authenticate: Basic realm="api"
Vary: Accept
Allow: GET, HEAD, OPTION

// odpowiedź: 

{
"detail": "Invalid username/password"
}
```
Uwierzytelnianie do API następuje poprzez podanie (metodą HTTP Basic authentication) loginu i hasła użytkownika analogicznych dla danych logowania do GUI panelu administracyjnego Sprzedawcy.
Po wywołaniu danego zasobu API zostanie zwrócona odpowiedź z odpowiednim kodem odpowiedzi HTTP. 



Jeśli zaistnieje taka potrzeba, to jak w powyższym przykładzie zostanie zwrócona własność detail z tekstowym opisem błędu.

Znaczenie zwracanych kodów odpowiedzi HTTP:

HTTP status code | OPIS
------------ | -------------
200 OK | ok
401 Unauthorized | nie udało się uwierzytelnić
403 Forbidden | brak uprawnień

# Format danych wejścia/wyjścia

> Przykładowo dla żadania zasobu: 

> https://ssl.dotpay.pl/test_seller/api/v1/accounts/

> z nagłówkami:

```json
GET /test_seller/api/v1/accounts/ HTTP/1.1
Host: ssl.dotpay.pl
Accept: application/json
Content-Type: application/json
```
```xml
GET /test_seller/api/v1/accounts/ HTTP/1.1
Host: ssl.dotpay.pl
Accept: application/xml
Content-Type: application/xml
```
> zostanie zwrócona odpowiedź:

```json
{
  "count": 1,
  "next": null,
  "previous": null,
  "results": [
    {
      "href": "https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/",
      "id": "443005",
      "status": "active",
      "name": "Hoan Kiem Megastore",
      "mcc_code": "7273",
      "main_url": "http://www.example.com/",
      "config": {
        "urlc": "http://www.example.com/confirmation/",
        "block_external_urlc": false,
        "pin": "eMhbAulyaQKnFORbRL2EwK0hHZ5C7rkX"
      }
    }
  ]
}
```

```xml
<root>
  <count>1</count>
  <next><next/>
  <previous><previous/>
  <results>
    <list-item>
      <href>https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/</href>
      <id>443005</id>
      <status>active</status>
      <name>Hoan Kiem Megastore</name>
      <mcc_code>7273</mcc_code>
      <main_url>http://www.example.com/</main_url>
      <config>
        <urlc>http://www.example.com/confirmation/</urlc>
        <block_external_urlc>False</block_external_urlc>
        <pin>eMhbAulyaQKnFORbRL2EwK0hHZ5C7rkX</pin>
      </config>
    </list-item>
  </results>
</root>
```

> W przypadku wywołania zasobów z parametrem format (jak w poniższych przykładach), API zwróci odpowiedź analogiczną do powyższych.

```xml
https://ssl.dotpay.pl/test_seller/api/v1/accounts/?format=xml
```

```json
https://ssl.dotpay.pl/test_seller/api/v1/accounts/?format=json
```

API ma możliwość komunikacji w formacie json (domyślnie) lub xml. Wybór formatowania odbywa się za pomocą przesłania nagłówków Accept oraz Content-Type lub dodatkowego parametru format (przekazanego metodą GET), którego wartością jest nazwa danego formatu (json lub xml).

## Kodowanie znaków
W przypadku niektórych zasobów, przesyłane do Dotpay żądania wymagają podania specyficznych informacji, przykładowo dla wypłat będą to dane odbiorcy. Aby uniknąć problemów ze znakami diakrytycznymi, należy pamiętać o ich odpowiednim kodowaniu (system Dotpay wykorzystuje UTF-8).

> Przykładowo funkcja json_encode z PHP domyślnie zamienia znaki diakrytyczne na format \uXXXX oraz stosuje znak ucieczki dla „/”:

```php
<?php
 $description = "Zażółć // Gęślą // Jaźń";
 $encoded = json_encode($description);
    echo "<pre>";
        echo "\nBefore json encode: ".$description;
        echo "\nAfter json encode: ".$encoded;
    echo "</pre>";
?>
```

>zwróci wynik:

```
Before json encode: Zażółć // Gęślą // Jaźń
After json encode: "Zażółć // Gęślą // Jaźń"
```

# Paginacja

> Przykładowo dla zasobu:
> https://ssl.dotpay.pl/test_seller/api/v1/operations/?page=2

```php
<?php
 $description = "Zażółć // Gęślą // Jaźń";
 $encoded = json_encode($description);
    echo "<pre>";
        echo "\nBefore json encode: ".$description;
        echo "\nAfter json encode: ".$encoded;
    echo "</pre>";
?>
```

> zostanie zwrócona odpowiedź jak poniższy fragment: 

```php
{
  "count": 538,
  "next": "https://ssl.dotpay.pl/test_seller/api/v1/operations/?page=3",
  "previous": "https://ssl.dotpay.pl/test_seller/api/v1/operations/",
  "results": [
    {
      "href": "https://ssl.dotpay.pl/test_seller/api/v1/operations/M1226-8222/",
      "number": "M1226-8222",
      "creation_datetime": "2015-04-29T10:28:25.187213",
      "type": "payment",
      "status": "new",
      "amount": "41.00",
      "currency": "PLN",
      "original_amount": "10.23",
      "original_currency": "EUR",
      "account_id": "443005",
      "related_operation": null,
      "description": "Zamowienie 839574",
      "control": "Bdt1Gc2Q2iRQ1EeW",
      "payer": {
        "first_name": "Jan",
        "last_name": "Kowalski",
        "email": "jan.kowalski@example.com"
      }
    },
    {
      "href": "https://ssl.dotpay.pl/test_seller/api/v1/operations/M1512-0298/",
      "number": "M1512-0298",
      "creation_datetime": "2015-04-28T10:59:33.753832",
      "type": "payment",
      "status": "completed",
      "amount": "1.00",
      "currency": "PLN",
      "original_amount": "1.00",
      "original_currency": "PLN",
      "account_id": "443005",
      "related_operation": null,
      "description": "Order 1265",
      "control": "ahh39mgmywm3wm4azzo18ja0y2fknzfz",
      "payer": {
        "first_name": "John",
        "last_name": "Smith",
        "email": "john.smith@example.com"
      }
    },

```

Dla żądań wymagających zwrócenia większej ilości danych odpowiedź API jest paginowana (domyślnie dla jednej strony wyświetlane jest maksymalnie 100 elementów). Odpowiedź zawiera własności next oraz previous, w których znajduje się adres kolejnej / poprzedniej strony odpowiedzi, natomiast we własności count znajduje się ilość obiektów zwróconych w całej odpowiedzi.

Znaczenie parametrów możliwych do przesyłania w żądaniu w celu filtrowania odpowiedzi:

Własność | Typ | Znaczenie/opis
------------ | -------------
page | int | numer strony
page_size | int | Ilość elementów wyświetlana na jednej stronie (min: 1 ; maks: 100)

# Zasoby (accounts)

## lista sklepów (ID) użytkownika

http://mateusztestowo.000webhostapp.com/a/build/index.html
