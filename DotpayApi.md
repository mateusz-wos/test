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

## Lista sklepów (ID) użytkownika

> nagłówki żądania: 

```
GET /test_seller/api/v1/accounts/ HTTP/1.1
Host: ssl.dotpay.pl
Accept: application/json
Authorization: Basic dXNlcjU4NDc6QVFNQWJxZEF2Qg==
Content-Type: application/json
```

> nagłówki odpowiedzi: 

```
HTTP/1.1 200 OK
Content-Type: application/json
Vary: Accept
Allow: GET, HEAD, OPTIONS
```

> odpowiedź: 

```php
{
  "count": 2,
  "next": null,
  "previous": null,
  "results": [
    {
      "href": "https://ssl.dotpay.pl/test_seller/api/v1/accounts/470707/",
      "id": "470707",
      "status": "active",
      "name": "HK Test 2",
      "mcc_code": "3709",
      "main_url": null,
      "config": {
        "urlc": "http://www.example.com/urlc_confirmation/",
        "block_external_urlc": true,
        "pin": "74fr6JxOy5jxJ2Qz"
      }
    },
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

> Poniżej został zamieszczony przykład żądania (oraz odpowiedzi) wykorzystujący język PHP oraz bibliotekę cURL.

```php
<?php

 $ch = curl_init();
  curl_setopt ($ch, CURLOPT_URL,"https://ssl.dotpay.pl/test_seller/api/v1/accounts/");
  curl_setopt ($ch, CURLOPT_SSL_VERIFYPEER, TRUE);
  curl_setopt ($ch, CURLOPT_SSL_VERIFYHOST, 2);
  curl_setopt ($ch, CURLOPT_CAINFO, "ca-bundle.crt"); //http://curl.haxx.se/docs/caextract.html
  curl_setopt ($ch, CURLOPT_FOLLOWLOCATION, 1);
  curl_setopt ($ch, CURLOPT_RETURNTRANSFER, 1);
  curl_setopt ($ch, CURLOPT_TIMEOUT, 100);
  curl_setopt ($ch, CURLOPT_USERPWD, 'user:password');

  $response = curl_exec($ch); // API response
  $curl_info = curl_getinfo($ch); //curl info
 curl_close($ch);

 echo "<pre>";
     echo "HTTP status code: ".$curl_info['http_code'];
     echo PHP_EOL."-------------------------".PHP_EOL.PHP_EOL;
     print_r(json_decode($response));
 echo "</pre>";
?>
```

> odpowiedź:

```
HTTP status code: 200
-------------------------

stdClass Object
(
 [count] => 2
 [next] => 
 [previous] => 
 [results] => Array
  (
   [0] => stdClass Object
    (
     [href] => https://ssl.dotpay.pl/test_seller/api/v1/accounts/470707/
     [id] => 470707
     [status] => active
     [name] => HK Test 2
     [mcc_code] => 3709
     [main_url] => 
     [config] => stdClass Object
      (
       [urlc] => http://www.example.com/urlc_confirmation/
       [block_external_urlc] => 1
       [pin] => 74fr6JxOy5jxJ2Qz
      )
    )
   [1] => stdClass Object
    (
     [href] => https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/
     [id] => 443005
     [status] => active
     [name] => Hoan Kiem Megastore
     [mcc_code] => 7273
     [main_url] => http://www.example.com/
     [config] => stdClass Object
      (
       [urlc] => http://www.example.com/confirmation/
       [block_external_urlc] => 
       [pin] => eMhbAulyaQKnFORbRL2EwK0hHZ5C7rkX
      )
    )
  )
)
```
Zasób zwraca listę wszystkich sklepów, do których zalogowany użytkownik posiada uprawnienia.

Własność | Typ | Znaczenie/opis
------------ | -------------
href | string | adres API, pod którym znajduje się odpowiedź dla danego sklepu (ID)
id | int | numer sklepu (ID)
status | string | status sklepu (ID)
name | string | nazwa sklepu (ID)
mcc_code | string | kod kategorii sprzedaży (MCC)
main_url | string | adres sprzedażowy sklepu (ID)
config.urlc | string | adres powiadomień urlc
config.block_external_urlc | bool | blokuj zewnętrzne urlc
config.pin | string | PIN

## Szczegóły sklepu (ID) 

> nagłówki żądania: 

```
GET /test_seller/api/v1/accounts/443005/ HTTP/1.1
Host: ssl.dotpay.pl
Accept: application/json
Authorization: Basic dXNlcjU4NDc6QVFNQWJxZEF2Qg==
Content-Type: application/json
```

> nagłówki odpowiedzi:

```
HTTP/1.1 200 OK
Content-Type: application/json
Vary: Accept
Allow: GET, HEAD, OPTIONS
```

> odpowiedź:

```php
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
```

> Poniżej został zamieszczony przykład żądania (oraz odpowiedzi) wykorzystujący język PHP oraz bibliotekę cURL.

```php
<?php

 $ch = curl_init();
 curl_setopt ($ch, CURLOPT_URL, "https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/");
  curl_setopt ($ch, CURLOPT_SSL_VERIFYPEER, TRUE);
  curl_setopt ($ch, CURLOPT_SSL_VERIFYHOST, 2);
  curl_setopt ($ch, CURLOPT_CAINFO, "ca-bundle.crt"); //http://curl.haxx.se/docs/caextract.html
  curl_setopt ($ch, CURLOPT_FOLLOWLOCATION, 1);
  curl_setopt ($ch, CURLOPT_RETURNTRANSFER, 1);
  curl_setopt ($ch, CURLOPT_TIMEOUT, 100);
  curl_setopt ($ch, CURLOPT_USERPWD, 'user:password');

  $response = curl_exec($ch); // API response
  $curl_info = curl_getinfo($ch); //curl info
 curl_close($ch);

 echo "<pre>";
     echo "HTTP status code: ".$curl_info['http_code'];
     echo PHP_EOL."-------------------------".PHP_EOL.PHP_EOL;
     print_r(json_decode($response));
 echo "</pre>";

?>
```

> odpowiedź:

```php
HTTP status code: 200
-------------------------

stdClass Object
(
 [href] => https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/
 [id] => 443005
 [status] => active
 [name] => Hoan Kiem Megastore
 [mcc_code] => 7273
 [main_url] => http://www.example.com/
 [config] => stdClass Object
  (
   [urlc] => http://www.example.com/confirmation/
   [block_external_urlc] => 
   [pin] => ueVBmSZa6ESmHi9V74fr6JxOy5jxJ2Qz
  )
)

```
Zasób zwraca szczegóły danego sklepu (ID) do którego zalogowany użytkownik posiada uprawnienia.

Znaczenie zwracanych kodów odpowiedzi HTTP:

HTTP status code | Opis 
------------ | -------------
200 OK | ok
404 Not Found | nie znaleziono sklepu

Własności zwrócone w odpowiedzi:

Własność | Typ | Znaczenie/opis
------------ | -------------
href | string | adres API, pod którym znajduje się odpowiedź dla danego sklepu (ID)
id | int | numer sklepu (ID)
status | string | status sklepu (ID)
name | string | nazwa sklepu (ID)
mcc_code | string | kod kategorii sprzedaży (MCC)
main_url | string | adres sprzedażowy sklepu (ID)
config.urlc | string | adres powiadomień urlc
config.block_external_urlc | bool | blokuj zewnętrzne urlc
config.pin | string | PIN

## Lista kanałów płatności sklepu (ID) 

> nagłówki żądania: 

```
GET /test_seller/api/v1/accounts/443005/channels/ HTTP/1.1
Host: ssl.dotpay.pl
Accept: application/json
Authorization: Basic dXNlcjU4NDc6QVFNQWJxZEF2Qg==
Content-Type: application/json
```

> nagłówki odpowiedzi: 

```
HTTP/1.1 200 OK
Content-Type: application/json
Vary: Accept
Allow: GET, HEAD, OPTIONS
```

> fragment odpowiedzi:

```
[
  {
    "id": 6,
    "name": "Przelew24",
    "logo": "https://ssl.dotpay.pl/test_payment/cloudfs2/magellan_media/payment_channel_logo/569d0b59dadfce09296b6be6/",
    "group": "fast_transfers",
    "is_blocked_by_seller": false,
    "is_disabled": false,
    "is_offline": false
  },
  {
    "id": 11,
    "name": "Bank transfer / postal",
    "logo": "https://ssl.dotpay.pl/test_payment/cloudfs2/magellan_media/payment_channel_logo/53b707a8dadfce7a89f5645c/",
    "group": "cash",
    "is_blocked_by_seller": false,
    "is_disabled": false,
    "is_offline": true
  },
  {
    "id": 248,
    "name": "Payment cards",
    "logo": "https://ssl.dotpay.pl/test_payment/cloudfs2/magellan_media/payment_channel_logo/5874ef1edadfce7e43444a10/",
    "group": "credit_cards",
    "is_blocked_by_seller": false,
    "is_disabled": false,
    "is_offline": false
  },
```

> Poniżej został zamieszczony przykład żądania (oraz odpowiedzi) wykorzystujący język PHP oraz bibliotekę cURL.

```php
<?php
 $ch = curl_init();
  curl_setopt ($ch, CURLOPT_URL, "https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/channels/");
  curl_setopt ($ch, CURLOPT_SSL_VERIFYPEER, TRUE);
  curl_setopt ($ch, CURLOPT_SSL_VERIFYHOST, 2);
  curl_setopt ($ch, CURLOPT_CAINFO, "ca-bundle.crt"); //http://curl.haxx.se/docs/caextract.html
  curl_setopt ($ch, CURLOPT_FOLLOWLOCATION, 1);
  curl_setopt ($ch, CURLOPT_RETURNTRANSFER, 1);
  curl_setopt ($ch, CURLOPT_TIMEOUT, 100);
  curl_setopt ($ch, CURLOPT_USERPWD, 'user:password');

  $response = curl_exec($ch); // API response
  $curl_info = curl_getinfo($ch); //curl info
 curl_close($ch);

 echo "<pre>";
     echo "HTTP status code: ".$curl_info['http_code'];
     echo PHP_EOL."-------------------------".PHP_EOL.PHP_EOL;
     print_r(json_decode($response));
 echo "</pre>";
?>
```

> Fragment odpowiedzi:

```
HTTP status code: 200
-------------------------

Array
(
 [0] => stdClass Object
    (
        [id] => 6
        [name] => Przelew24
        [logo] => https://ssl.dotpay.pl/test_payment/cloudfs2/magellan_media/payment_channel_logo/569d0b59dadfce09296b6be6/
        [group] => fast_transfers
        [is_blocked_by_seller] => 
        [is_disabled] => 
        [is_offline] => 
    )
 [1] => stdClass Object
    (
        [id] => 11
        [name] => Bank transfer / postal
        [logo] => https://ssl.dotpay.pl/test_payment/cloudfs2/magellan_media/payment_channel_logo/53b707a8dadfce7a89f5645c/
        [group] => cash
        [is_blocked_by_seller] => 
        [is_disabled] => 
        [is_offline] => 1
    )
 [2] => stdClass Object
    (
        [id] => 248
        [name] => Payment cards
        [logo] => https://ssl.dotpay.pl/test_payment/cloudfs2/magellan_media/payment_channel_logo/5874ef1edadfce7e43444a10/
        [group] => credit_cards
        [is_blocked_by_seller] => 
        [is_disabled] => 
        [is_offline] => 
    )
```

Zasób zwraca listę dostępnych kanałów płatności dla danego sklepu (ID).

Znaczenie zwracanych kodów odpowiedzi HTTP:

HTTP status code | Opis 
------------ | -------------
200 OK | ok
404 Not Found | nie znaleziono sklepu

Własności zwrócone w odpowiedzi:

Własność | Typ | Znaczenie/opis
------------ | -------------
id | int | numer kanału płatności
name | string | nazwa kanału płatności
logo | string | adres url, pod którym znajduje się logotyp kanału płatności
group | string | grupa do jakiej należy kanał płatności
is_blocked_by_seller | bool | blokada kanału przez Sprzedawcę
is_disabled | bool | kanał wyłączony
is_offline | bool | kanał w trybie offline, tj. nie jest w stanie zaksięgować płatności w czasie rzeczywistym

## Utworzenie linku płatniczego

> nagłówki żądania: 

```php
POST /test_seller/api/v1/accounts/443005/payment_links/ HTTP/1.1
Host: ssl.dotpay.pl
Accept: application/json
Authorization: Basic dXNlcjU4NDc6QVFNQWJxZEF2Qg==
Content-Type: application/json
```

> żądanie:

```php
{
  "amount": "99.11",
  "currency": "PLN",
  "description": "Payment for 586930 order",
  "control": "202cb962ac59075b964b07152d234b70",
  "language": "pl",
  "channel_id": 4,
  "ch_lock": "1",
  "onlinetransfer": "1",
  "redirection_type": "0",
  "buttontext": "return",
  "url": "http://www.example.com/thanks_page.php",
  "urlc": "http://www.example.com/confirmation/",
  "expiration_datetime": "2015-12-01T16:48:00",
  "payer": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "john.smith@example.com",
    "phone": "+48123456789",
    "address": {
      "street": "Wielicka",
      "building_number": 72,
      "postcode": "30-552",
      "city": "Krakow",
      "region": "Malopolska",
      "country": "POL"
    }
  },
  "recipient": {
    "account_number": "PL92942215610877228680536980",
    "company": "PJ Shop",
    "first_name": "Patrick",
    "last_name": "Jones",
    "address": {
      "street": "Marszalkowska",
      "building_number": 100,
      "postcode": "00-576",
      "city": "Warszawa"
    }
  }
}
```

> nagłówki odpowiedzi:

```
HTTP/1.1 201 Created
Content-Type: application/json
Vary: Accept
Allow: GET, POST, HEAD, OPTIONS
Location: https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/payment_links/5e60d3r728trixvagqj7bds19r0irm31/
```

> odpowiedź:

```php
{
  "href": "https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/
    payment_links/5e60d3r728trixvagqj7bds19r0irm31/",
  "payment_url": "https://ssl.dotpay.pl/test_payment/
    ?pid=5e60d3r728trixvagqj7bds19r0irm31",
  "token": "5e60d3r728trixvagqj7bds19r0irm31",
  "amount": "99.11",
  "currency": "PLN",
  "description": "Payment for 586930 order",
  "control": "202cb962ac59075b964b07152d234b70",
  "language": "pl",
  "channel_id": 4,
  "ch_lock": true,
  "onlinetransfer": true,
  "redirection_type": 0,
  "buttontext": "return",
  "url": "http://www.example.com/thanks_page.php",
  "urlc": "http://www.example.com/confirmation/",
  "expiration_datetime": "2015-12-01T16:48:00",
  "payer": {
    "first_name": "John",
    "last_name": "Smith",
    "email": "john.smith@example.com",
    "phone": "+48123456789",
    "address": {
      "street": "Wielicka",
      "building_number": "72",
      "flat_number": null,
      "postcode": "30-552",
      "city": "Krakow",
      "region": "Malopolska",
      "country": "POL"
    }
  },
  "recipient": {
    "account_number": "PL92 9422 1561 0877 2286 8053 6980",
    "company": "PJ Shop",
    "first_name": "Patrick",
    "last_name": "Jones",
    "address": {
      "street": "Marszalkowska",
      "building_number": "100",
      "flat_number": null,
      "postcode": "00-576",
      "city": "Warszawa"
    }
  }
}
```
> Poniżej został zamieszczony przykład żądania (oraz odpowiedzi) wykorzystujący język PHP oraz bibliotekę cURL

```php
<?php
 $fields = array(
  'amount' => '99.11',
  'currency' => 'PLN',
  'description' => 'Payment for 586930 order',
  'control' => '202cb962ac59075b964b07152d234b70',
  'language' => 'pl',
  'channel_id' => 4,
  'ch_lock' => "1",
  'onlinetransfer' => "1",
  'redirection_type' => "0",
  'buttontext' => "return",
  'url' => 'http://www.example.com/thanks_page.php',
  'urlc' => 'http://www.example.com/confirmation/',
  'expiration_datetime' => '2015-12-01T16:48:00',
  'payer' => array(
    'first_name' => 'John',
    'last_name' => 'Smith',
    'email' => 'john.smith@example.com',
    'phone' => '+48123456789',
    'address' => array(
      'street' => 'Wielicka',
      'building_number' => 72,
      'postcode' => '30-552',
      'city' => 'Krakow',
      'region' => 'Malopolska',
      'country' => 'POL')
      ),
  'recipient' => array(
    'account_number' => 'PL92942215610877228680536980',
    'company' => 'PJ Shop',
    'first_name' => 'Patrick',
    'last_name' => 'Jones',
    'address' => array(
      'street' => 'Marszalkowska',
      'building_number' => 100,
      'postcode' => '00-576',
      'city' => 'Warszawa')
      ),
    );
  
 $data=json_encode($fields, 320);
 $ch = curl_init();
  curl_setopt ($ch, CURLOPT_URL, "https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/payment_links/");
  curl_setopt ($ch, CURLOPT_SSL_VERIFYPEER, TRUE);
  curl_setopt ($ch, CURLOPT_SSL_VERIFYHOST, 2);
  curl_setopt ($ch, CURLOPT_CAINFO, "ca-bundle.crt"); //http://curl.haxx.se/docs/caextract.html
  curl_setopt ($ch, CURLOPT_FOLLOWLOCATION, 1);
  curl_setopt ($ch, CURLOPT_RETURNTRANSFER, 1);
  curl_setopt ($ch, CURLOPT_TIMEOUT, 100);
  curl_setopt ($ch, CURLOPT_USERPWD, 'user:password');
  curl_setopt ($ch, CURLOPT_POST, 1);
  curl_setopt ($ch, CURLOPT_POSTFIELDS, $data);
  curl_setopt ($ch, CURLOPT_HTTPHEADER, array(
    'Content-Type: application/json',
    'Content-Length: '.strlen($data)));

    $response = curl_exec($ch); // API response
    $curl_info = curl_getinfo($ch); //curl info
  curl_close($ch);

 echo "<pre>";
     echo "HTTP status code: ".$curl_info['http_code'];
     echo PHP_EOL."-------------------------".PHP_EOL.PHP_EOL;
     print_r(json_decode($response));
 echo "</pre>";
?>
```

> odpowiedź:

```php
HTTP status code: 201
-------------------------

stdClass Object
(
  [href] => https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/
    payment_links/5e60d3r728trixvagqj7bds19r0irm31/
  [payment_url] => https://ssl.dotpay.pl/test_payment/
    ?pid=5e60d3r728trixvagqj7bds19r0irm31
  [token] => 5e60d3r728trixvagqj7bds19r0irm31
  [amount] => 99.11
  [currency] => PLN
  [description] => Payment for 586930 order
  [control] => 202cb962ac59075b964b07152d234b70
  [language] => pl
  [channel_id] => 4
  [ch_lock] => 1
  [onlinetransfer] => 1
  [redirection_type] => 0
  [buttontext] => return
  [url] => http://www.example.com/thanks_page.php
  [urlc] => http://www.example.com/confirmation/
  [expiration_datetime] => 2015-12-01T16:48:00
  [payer] => stdClass Object
    (
      [first_name] => John
      [last_name] => Smith
      [email] => john.smith@example.com
      [phone] => +48123456789
      [address] => stdClass Object
        (
          [street] => Wielicka
          [building_number] => 72
          [flat_number] => 
          [postcode] => 30-552
          [city] => Krakow
          [region] => Malopolska
          [country] => POL
        )
    )
  [recipient] => stdClass Object
    (
      [account_number] => PL92 9422 1561 0877 2286 8053 6980
      [company] => PJ Shop
      [first_name] => Patrick
      [last_name] => Jones
      [address] => stdClass Object
        (
          [street] => Marszalkowska
          [building_number] => 100
          [flat_number] => 
          [postcode] => 00-576
          [city] => Warszawa
        )
    )
)
```

Zasób pozwala stworzyć link płatniczy dla danego sklepu (ID).

Znaczenie zwracanych kodów odpowiedzi HTTP:

HTTP status code | Opis 
------------ | -------------
201 Created | utworzono link
404 Not Found | nie znaleziono sklepu

Znaczenie własności przesyłanych w żądaniu (z wyjątkiem href, payment_url, token) oraz zwracanych w odpowiedzi:

Własność | Typ | Znaczenie/opis
------------ | -------------
href | string | adres API, pod którym znajdują się szczegóły utworzonego linku
payment_url | string | wygenerowany link płatniczy
token | string | token identyfikujący płatność
amount | decimal | decimal
currency | string | waluta - format: ISO 4217
description | string | opis płatności
control | string | parametr pozwalający na przechowanie ciągu (np. numeru zamówienia ze sklepu Sprzedawcy) o długości do 1000 znaków. Parametr w formie niezmienionej jest odsyłany do serwisu Sprzedawcy w powiadomieniu URLC
language | string | język - format: ISO 639-1
channel_id | string | numer kanału płatności
ch_lock | string | zablokowanie określonego kanału płatności
onlinetransfer | string | sposób wyświetlenia kanałów offline na stronie płatności - UWAGA: parametr tymczasowo niedostępny
redirection_type | string | metoda odwołania do serwisu Sprzedawcy po dokonanej płatności
buttontext | string | treść przycisku powrotu do serwisu sprzedawcy
url | string | adres na który jest realizowany powrót do Sprzedawcy
urlc | string | adres na który zostanie przesłana notyfikacja URLC
expiration_datetime | string | data ważności linku - format: YYYY-MM-DDTHH:MM:SS
payer.first_name | string | imię osoby płacącej
payer.last_name | string | nazwisko osoby płacącej
payer.email | string | email osoby płacącej
payer.phone | string | telefon osoby płacącej
payer.address.street | string | ulica
payer.address.building_number | string | numer budynku
payer.address.flat_number | string | numer mieszkania
payer.address.postcode | string | kod pocztowy
payer.address.city | string | miasto
payer.address.region | string | region
payer.address.country | string | państwo - format: ISO 3166-1 alfa-3
recipient.account_number | string | numer rachunku odbiorcy płatności - format: IBAN
recipient.company | string | nazwa firmy odbiorcy płatności
recipient.first_name | string | imię odbiorcy płatności
recipient.last_name | string | nazwisko odbiorcy płatności
recipient.address.street | string | ulica
recipient.address.building_number | string | numer budynku
recipient.address.flat_number | string | numer mieszkania
recipient.address.postcode | string | kod pocztowy
recipient.address.city | string | miasto

## Usunięcie linku płatniczego

> nagłówki żądania: 

```
DELETE /test_seller/api/v1/accounts/443005/payment_links/5e60d3r728trixvagqj7bds19r0irm31/ HTTP/1.1
Host: ssl.dotpay.pl
Accept: application/json
Authorization: Basic dXNlcjU4NDc6QVFNQWJxZEF2Qg==
Content-Type: application/json
```

> nagłówki odpowiedzi:

```
HTTP/1.1 204 No Content
Content-Type: application/json
Vary: Accept
Allow: GET, PUT, PATCH, DELETE, HEAD, OPTIONS
```

> Poniżej został zamieszczony przykład żądania (oraz odpowiedzi) wykorzystujący język PHP oraz bibliotekę cURL

```php
<?php

 $ch = curl_init();
 curl_setopt ($ch, CURLOPT_URL, "https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/payment_links/5e60d3r728trixvagqj7bds19r0irm31/");
  curl_setopt ($ch, CURLOPT_SSL_VERIFYPEER, TRUE);
  curl_setopt ($ch, CURLOPT_SSL_VERIFYHOST, 2);
  curl_setopt ($ch, CURLOPT_CAINFO, "ca-bundle.crt"); //http://curl.haxx.se/docs/caextract.html
  curl_setopt ($ch, CURLOPT_FOLLOWLOCATION, 1);
  curl_setopt ($ch, CURLOPT_RETURNTRANSFER, 1);
  curl_setopt ($ch, CURLOPT_TIMEOUT, 100);
  curl_setopt ($ch, CURLOPT_USERPWD, 'user:password');
  curl_setopt ($ch, CURLOPT_CUSTOMREQUEST, "DELETE");

  $response = curl_exec($ch); // API response
  $curl_info = curl_getinfo($ch); //curl info
 curl_close($ch);

 echo "<pre>";
     echo "HTTP status code: ".$curl_info['http_code'];
     echo PHP_EOL."-------------------------".PHP_EOL.PHP_EOL;
     print_r(json_decode($response));
 echo "</pre>";

?>
```

> odpowiedź:

```
HTTP status code: 201
-------------------------
```

Zasób pozwala usunąć stworzony link płatniczy dla danego sklepu (ID).

HTTP status code | Opis 
------------ | -------------
204 No Content | usunięto link
404 Not Found | nie znaleziono sklepu

## Lista linków płatniczych

> nagłówki żądania: 

```
GET /test_seller/api/v1/accounts/443005/payment_links/ HTTP/1.1
Host: ssl.dotpay.pl
Accept: application/json
Authorization: Basic dXNlcjU4NDc6QVFNQWJxZEF2Qg==
Content-Type: application/json
```

> nagłówki odpowiedzi:

```
HTTP/1.1 200 OK
Content-Type: application/json
Vary: Accept
Allow: GET, POST, HEAD, OPTIONS
```

> fragment odpowiedzi:

```php
{
  "count": 116,
  "next": "https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/
    payment_links/?page=2",
  "previous": null,
  "results": [
    {
      "href": "https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/
        payment_links/3drilk63fknyko778yziran6yx603xvo/",
      "payment_url": "https://ssl.dotpay.pl/test_payment/
        ?pid=3drilk63fknyko778yziran6yx603xvo",
      "token": "3drilk63fknyko778yziran6yx603xvo",
      "amount": "99.11",
      "currency": "PLN",
      "description": "Payment for 586930 order",
      "control": "202cb962ac59075b964b07152d234b70",
      "language": "pl",
      "channel_id": 4,
      "ch_lock": true,
      "onlinetransfer": true,
      "redirection_type": 0,
      "url": "http://www.example.com/thanks_page.php",
      "urlc": "http://www.example.com/confirmation/",
      "expiration_datetime": "2015-12-01T16:48:00",
      "payer": {
        "first_name": "John",
        "last_name": "Smith",
        "email": "john.smith@example.com",
        "phone": "+48123456789",
        "address": {
          "street": "Wielicka",
          "building_number": "72",
          "flat_number": null,
          "postcode": "30-552",
          "city": "Krakow",
          "region": "Malopolska",
          "country": "POL"
        }
      },
      "recipient": {
        "account_number": "PL92 9422 1561 0877 2286 8053 6980",
        "company": "PJ Shop",
        "first_name": "Patrick",
        "last_name": "Jones",
        "address": {
          "street": "Marszalkowska",
          "building_number": "100",
          "flat_number": null,
          "postcode": "00-576",
          "city": "Warszawa"
        }
      }
    },
    {
      "href": "https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/
        payment_links/jov2viovv0tlz22wwy2svtunb2sm5x4o/",
      "payment_url": "https://ssl.dotpay.pl/test_payment/
        ?pid=jov2viovv0tlz22wwy2svtunb2sm5x4o",
      "token": "jov2viovv0tlz22wwy2svtunb2sm5x4o",
      "amount": "99.11",
      "currency": "PLN",
      "description": "platnosc dla Jan Kowalski",
      "control": null,
      "language": "pl",
      "channel_id": null,
      "ch_lock": null,
      "onlinetransfer": null,
      "redirection_type": 0,
      "url": "http://www.example.com/",
      "urlc": null,
      "expiration_datetime": null,
      "payer": {
        "first_name": "John",
        "last_name": "Smith",
        "email": "j.smith@example.com",
        "phone": null,
        "address": {
          "street": "Warszawska",
          "building_number": "1",
          "flat_number": null,
          "postcode": "11-111",
          "city": "Krakow",
          "region": null,
          "country": "POL"
        }
      },
      "recipient": {
        "account_number": "",
        "company": null,
        "first_name": null,
        "last_name": null,
        "address": {
          "street": null,
          "building_number": null,
          "flat_number": null,
          "postcode": null,
          "city": null
        }
      }
    },
```

> Poniżej został zamieszczony przykład żądania (oraz odpowiedzi) wykorzystujący język PHP oraz bibliotekę cURL.

```php
<?php

 $ch = curl_init();
  curl_setopt ($ch, CURLOPT_URL, "https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/payment_links/");
  curl_setopt ($ch, CURLOPT_SSL_VERIFYPEER, TRUE);
  curl_setopt ($ch, CURLOPT_SSL_VERIFYHOST, 2);
  curl_setopt ($ch, CURLOPT_CAINFO, "ca-bundle.crt"); //http://curl.haxx.se/docs/caextract.html
  curl_setopt ($ch, CURLOPT_FOLLOWLOCATION, 1);
  curl_setopt ($ch, CURLOPT_RETURNTRANSFER, 1);
  curl_setopt ($ch, CURLOPT_TIMEOUT, 100);
  curl_setopt ($ch, CURLOPT_USERPWD, 'user:password');

  $response = curl_exec($ch); // API response
  $curl_info = curl_getinfo($ch); //curl info
 curl_close($ch);
 echo "<pre>";
     echo "HTTP status code: ".$curl_info['http_code'];
     echo PHP_EOL."-------------------------".PHP_EOL.PHP_EOL;
     print_r(json_decode($response));
 echo "</pre>";

?>
```

> Fragment odpowiedzi:

```php
HTTP status code: 200
-------------------------

stdClass Object
(
  [count] => 116
  [next] => https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/
    payment_links/?page=2
  [previous] => 
  [results] => Array
    (
      [0] => stdClass Object
        (
          [href] => https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/
            payment_links/3drilk63fknyko778yziran6yx603xvo/
          [payment_url] => https://ssl.dotpay.pl/test_payment/
            ?pid=3drilk63fknyko778yziran6yx603xvo
          [token] => 3drilk63fknyko778yziran6yx603xvo
          [amount] => 99.11
          [currency] => PLN
          [description] => Payment for 586930 order
          [control] => 202cb962ac59075b964b07152d234b70
          [language] => pl
          [channel_id] => 4
          [ch_lock] => 1
          [onlinetransfer] => 1
          [redirection_type] => 0
          [url] => http://www.example.com/thanks_page.php
          [urlc] => http://www.example.com/confirmation/
          [expiration_datetime] => 2015-12-01T16:48:00
          [payer] => stdClass Object
            (
              [first_name] => John
              [last_name] => Smith
              [email] => john.smith@example.com
              [phone] => +48123456789
              [address] => stdClass Object
                (
                  [street] => Wielicka
                  [building_number] => 72
                  [flat_number] => 
                  [postcode] => 30-552
                  [city] => Krakow
                  [region] => Malopolska
                  [country] => POL
                ) 
            )
          [recipient] => stdClass Object
            (
              [account_number] => PL92 9422 1561 0877 2286 8053 6980
              [company] => PJ Shop
              [first_name] => Patrick
              [last_name] => Jones
              [address] => stdClass Object
                (
                  [street] => Marszalkowska
                  [building_number] => 100
                  [flat_number] => 
                  [postcode] => 00-576
                  [city] => Warszawa
                ) 
            ) 
        )
      [1] => stdClass Object
        (
           [href] => https://ssl.dotpay.pl/test_seller/api/v1/accounts/443005/
             payment_links/jov2viovv0tlz22wwy2svtunb2sm5x4o/
           [payment_url] => https://ssl.dotpay.pl/test_payment/
             ?pid=jov2viovv0tlz22wwy2svtunb2sm5x4o
           [token] => jov2viovv0tlz22wwy2svtunb2sm5x4o
           [amount] => 99.11
           [currency] => PLN
           [description] => platnosc dla Jan Kowalski
           [control] => 
           [language] => pl
           [channel_id] => 
           [ch_lock] => 
           [onlinetransfer] => 
           [redirection_type] => 0
           [url] => http://www.example.com/
           [urlc] => 
           [expiration_datetime] => 
           [payer] => stdClass Object
            (
               [first_name] => John
               [last_name] => Smith
               [email] => j.smith@example.com
               [phone] => 
               [address] => stdClass Object
                (
                   [street] => Warszawska
                   [building_number] => 1
                   [flat_number] => 
                   [postcode] => 11-111
                   [city] => Krakow
                   [region] => 
                   [country] => POL
                ) 
            )
           [recipient] => stdClass Object
            (
              [account_number] => 
              [company] => 
              [first_name] => 
              [last_name] => 
              [address] => stdClass Object
                (
                  [street] => 
                  [building_number] => 
                  [flat_number] => 
                  [postcode] => 
                  [city] => 
                ) 
            )
        )
```

Zasób zwraca listę linków płatniczych dla danego sklepu (ID).

Znaczenie zwracanych kodów odpowiedzi HTTP:

HTTP status code | Opis 
------------ | -------------
200 OK | ok
404 Not Found | nie znaleziono sklepu
