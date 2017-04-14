# WSTĘP

Niniejszy dokument opisuje interfejs systemu płatności oferowanego przez serwis dotpay i przeznaczony jest zarówno dla podmiotów 
zainteresowanych ofertą serwisu, jak i osób zajmujących się wdrożeniem systemu.

# CHARAKTERYSTYKA I ADRES INTERFEJSU

API panelu administracyjnego Sprzedawcy został wykonany w stylu architektury REST

Adresy pod którymi dostępny jest interfejs to:

Środowisko produkcyjne:
`https://ssl.dotpay.pl/s2/login/api/`
Środowisko testowe:
`https://ssl.dotpay.pl/test_seller/api/`

# UWIERZYTELNIANIE I AUTORYZACJA
Uwierzytelnianie do API następuje poprzez podanie (metodą HTTP Basic authentication) loginu i hasła użytkownika analogicznych dla danych logowania do GUI panelu administracyjnego Sprzedawcy.
Po wywołaniu danego zasobu API zostanie zwrócona odpowiedź z odpowiednim kodem odpowiedzi HTTP. 

Przykładowo dla zasobu:
`https://ssl.dotpay.pl/test_seller/api/v1/accounts/`

po wykonaniu żądania z błędnym loginem / hasłem:
+ nagłówki żądania: 

       GET /test_seller/api/v1/accounts/ HTTP/1.1
       Host: ssl.dotpay.pl
       Accept: application/json
       Content-Type: application/json
       Authorization: Basic VXNlcjU4NDc6QVFNQWJxZEF2Yg==

zostanie zwrócona odpowiedź:
+ nagłówki odpowiedzi: 

       HTTP/1.1 401 Unauthorized
       Content-Type: application/json
       WWW-Authenticate: Basic realm="api"
       Vary: Accept
       Allow: GET, HEAD, OPTIONS
+ odpowiedź:: 

       {
        "detail": "Invalid username/password"
       }

Jeśli zaistnieje taka potrzeba, to jak w powyższym przykładzie zostanie zwrócona własność detail z tekstowym opisem błędu.

>Znaczenie zwracanych kodów odpowiedzi HTTP:

HTTP status code | OPIS
------------ | -------------
200 OK | ok
401 Unauthorized | nie udało się uwierzytelnić
403 Forbidden | brak uprawnień


## Questions Collection [/questions]

### List All Questions [GET]

+ Response 200 (application/json)

       GET /test_seller/api/v1/accounts/ HTTP/1.1
       Host: ssl.dotpay.pl
       Accept: application/json
       Content-Type: application/json
       Authorization: Basic VXNlcjU4NDc6QVFNQWJxZEF2Yg==


### Create a New Question [POST]

You may create your own question using this action. It takes a JSON
object containing a question and a collection of answers in the
form of choices.

+ Request (application/json)

        {
            "question": "Favourite programming language?",
            "choices": [
                "Swift",
                "Python",
                "Objective-C",
                "Ruby"
            ]
        }

+ Response 201 (application/json)

    + Headers

            Location: /questions/2

    + Body

            {
                "question": "Favourite programming language?",
                "published_at": "2015-08-05T08:40:51.620Z",
                "choices": [
                    {
                        "choice": "Swift",
                        "votes": 0
                    }, {
                        "choice": "Python",
                        "votes": 0
                    }, {
                        "choice": "Objective-C",
                        "votes": 0
                    }, {
                        "choice": "Ruby",
                        "votes": 0
                    }
                ]
            }
