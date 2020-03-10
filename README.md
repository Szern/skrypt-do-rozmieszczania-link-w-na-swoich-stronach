# skrypt-do-rozmieszczania-link-w-na-swoich-stronach
Właściwie bardziej koncept niż skrypt, ale działający.

Możemy rozmieszczać linki na stronach php. W plikach html skrypt nie zadziała.

Na każdej stronie w miejscu kodu, w którym mają się znaleźć linki umieszczamy poniższy kod:

<?php

    $znacznik = "twoja.pierwsza.strona";
    $headers = array(
        "GET http://www.twojastrona.pl/pilot.php HTTP/1.1",
        "Host:  twojastrona.pl",
        "User-Agent: Mozilla/3.0 (compatible)",
        "Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
        "Accept-Language: pl,en-us;q=0.7,en;q=0.3",
        "Accept-Encoding: gzip,deflate",
        "Accept-Charset: ISO-8859-2,utf-8;q=0.7,*;q=0.7",
        "Connection: keep-alive",
    );

    $ch  = curl_init("http://www.twojastrona.pl/pilot.php");
    curl_setopt($ch, CURLOPT_TIMEOUT, 0);
    curl_setopt($ch, CURLOPT_HEADER, $headers);
    curl_setopt($ch, CURLOPT_BINARYTRANSFER, true);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $res = curl_exec($ch);
    curl_close($ch);
    
    $res = substr($res, (strpos($res, "poczatek")) + 8);
    $res = substr($res, 0, (strpos($res, "koniec")));
    $linki = explode("\n", $res);
    for ($x = 0; $x < count($linki); $x++) {
        $link = explode("$", $linki[$x]);
        if ($link[0] == $znacznik) {
            echo '<li>' . $link[1] . '</li>';
        }
    }

?>

Wyjaśnienia:
1) wiersz:

$znacznik = "twoja.pierwsza.strona";
zawiera unikalny identyfikator strony (lub miejsca, gdzie pojawiają się linki).
Dla każdej strony zmieniamy ten identyfikator.
2) wiersze:

        "GET http://www.twojastrona.pl/pilot.php HTTP/1.1",
        "Host:  twojastrona.pl"

$ch  = curl_init("http://www.twojastrona.pl/pilot.php");
Zawierają położenie pliku sterującego linkami (może być na innym serwerze itp.).
Ten plik to pilot.php.

Zawartość pliku pilot.php:

poczatek
twoja.pierwsza.strona$<a href="http://domena.pl" title="domena">Domena</a>
twoja.druga.strona$<a href="http://domena.pl" title="domena">Domena</a>
twoja.druga.strona$<a href="http://domena.com" title="domena Com">Com</a>
inna.strona$<a href="http://domena.pl" title="domena">Domena</a>
koniec

Pierwszy i ostatni wiersz (początek i koniec) są ważne, plik musi je zawierać.
W kolejnych wierszach wstawiamy:
1) unikalny identyfikator strony (występujący w skrypcie powyżej),
2) znak dolara $
3) kod linku.

Może w nim być dowolna ilość stron, na jednej stronie może być dowolna ilość linków.

Uwaga, plik wyświetla linki jeden pod drugim, pod warunkiem, że pierwszy fragment kodu zawarty jest w znacznikach <ul>wstawka</ul>. Jeśli chcesz zmienić wyświetlanie linków np. na poziome, należy wyedytować ten wiersz:

echo '<li>' . $link[1] . '</li>';
zmieniając go na przykład na:

echo $link[1] . ' | ';
