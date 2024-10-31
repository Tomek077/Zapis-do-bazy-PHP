# Prosty Tutorial: Jak Zapisać Dane do Bazy Danych MySQL na Jednej Stronie z Wykorzystaniem PHP (Podejście Proceduralne)

## Wstęp

W tym tutorialu nauczysz się, jak na **jednej stronie** stworzyć formularz i zapisać dane do bazy danych MySQL przy użyciu PHP w **podejściu proceduralnym**. Skupimy się na prostym i czytelnym kodzie, który będzie łatwy do zrozumienia.

## Co Będzie Potrzebne?

- **Serwer WWW** z obsługą PHP (np. XAMPP)
- **Baza danych MySQL**
- **Edytor tekstu** (np. Notatnik)

## Krok 1: Utworzenie Bazy Danych i Tabeli

1. **Uruchom phpMyAdmin** (lub inne narzędzie do zarządzania MySQL).
2. **Stwórz nową bazę danych** o nazwie `moja_baza`.
3. **Utwórz tabelę** o nazwie `uzytkownicy` z polami:
   - `id` (typ: INT, auto_increment, klucz główny)
   - `imie` (typ: VARCHAR(50))
   - `email` (typ: VARCHAR(50))

## Krok 2: Stworzenie Pliku PHP z Formularzem i Zapisywaniem Danych

Utwórz plik o nazwie `formularz.php` i wklej poniższy kod:

```php
<!DOCTYPE html>
<html>
<head>
    <title>Formularz</title>
</head>
<body>

<?php
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    // Dane do połączenia z bazą
    $host = "localhost";
    $uzytkownik = "root";
    $haslo = "";
    $baza = "moja_baza";

    // Połączenie z bazą danych
    $polaczenie = mysqli_connect($host, $uzytkownik, $haslo, $baza);

    // Sprawdzenie połączenia
    if (!$polaczenie) {
        die("Błąd połączenia: " . mysqli_connect_error());
    }

    // Pobranie danych z formularza
    $imie = $_POST['imie'];
    $email = $_POST['email'];

    // Zapytanie SQL
    $sql = "INSERT INTO uzytkownicy (imie, email) VALUES ('$imie', '$email')";

    // Wykonanie zapytania
    mysqli_query($polaczenie, $sql);

    // Zamknięcie połączenia
    mysqli_close($polaczenie);

    // Komunikat
    echo "Dane zostały zapisane.<br><br>";
}
?>

<form action="formularz.php" method="post">
    Imię: <input type="text" name="imie"><br><br>
    Email: <input type="text" name="email"><br><br>
    <input type="submit" value="Wyślij">
</form>

</body>
</html>
```

## Wyjaśnienie Kodu

- **Cały kod** jest umieszczony w jednym pliku `formularz.php`.
- **Sprawdzamy**, czy formularz został przesłany za pomocą `$_SERVER['REQUEST_METHOD'] == 'POST'`.
- **Łączymy się** z bazą danych MySQL używając funkcji proceduralnej `mysqli_connect`.
- **Pobieramy dane** z formularza za pomocą `$_POST['imie']` i `$_POST['email']`.
- **Tworzymy zapytanie SQL**, które wstawia dane do tabeli `uzytkownicy`.
- **Wykonujemy zapytanie** za pomocą `mysqli_query`.
- **Zamykamy połączenie** z bazą danych.
- **Wyświetlamy formularz**, aby użytkownik mógł wprowadzić dane.

## Krok 3: Testowanie

1. **Uruchom przeglądarkę internetową**.
2. Przejdź pod adres: `http://localhost/formularz.php`.
3. **Wypełnij formularz** i kliknij "Wyślij".
4. Powinien pojawić się komunikat: "Dane zostały zapisane."

## Podsumowanie

Gratulacje! Udało Ci się stworzyć prostą stronę, która umożliwia zapisywanie danych do bazy MySQL przy użyciu PHP w podejściu proceduralnym.

---

**Uwaga:** W tym przykładzie nie dodaliśmy zabezpieczeń przed potencjalnymi błędami lub atakami, takimi jak **SQL Injection**. W praktyce warto użyć **przygotowanych instrukcji (prepared statements)** lub funkcji takich jak `mysqli_real_escape_string`, aby zwiększyć bezpieczeństwo aplikacji.

---

# Dodatkowe Informacje dla Zainteresowanych

Jeśli chcesz poprawić bezpieczeństwo swojego skryptu, możesz zmodyfikować kod, aby użyć funkcji `mysqli_real_escape_string`:

```php
// Pobranie i zabezpieczenie danych z formularza
$imie = mysqli_real_escape_string($polaczenie, $_POST['imie']);
$email = mysqli_real_escape_string($polaczenie, $_POST['email']);
```

Lub lepiej, użyj **przygotowanych instrukcji**:

```php
// Przygotowanie zapytania SQL
$stmt = mysqli_prepare($polaczenie, "INSERT INTO uzytkownicy (imie, email) VALUES (?, ?)");

// Powiązanie parametrów
mysqli_stmt_bind_param($stmt, "ss", $imie, $email);

// Wykonanie zapytania
mysqli_stmt_execute($stmt);

// Zamknięcie instrukcji
mysqli_stmt_close($stmt);
```

W ten sposób zabezpieczysz swoją aplikację przed potencjalnymi atakami i błędami.

---
Funkcja `mysqli_stmt_bind_param` służy do powiązania zmiennych z parametrami zapytania SQL w przygotowanym wyrażeniu (ang. *prepared statement*), dzięki czemu można bezpiecznie przekazywać dane do zapytania SQL. W Twoim przykładzie:

```php
mysqli_stmt_bind_param($stmt, "ss", $imie, $email);
```

1. **Pierwszy parametr (`$stmt`)** – to identyfikator przygotowanego zapytania, które wcześniej stworzyłeś za pomocą `mysqli_prepare()`. Jest to zmienna reprezentująca całe zapytanie SQL w wersji "do przygotowania".

2. **Drugi parametr (`"ss"`)** – oznacza typy danych, które będą przekazane do zapytania SQL. Każda litera odnosi się do typu danych:
   - `s` – oznacza `string`, czyli tekst.
   - `i` – oznacza `integer`, czyli liczby całkowite.
   - `d` – oznacza `double`, czyli liczby zmiennoprzecinkowe.
   - `b` – oznacza dane binarne.

   W tym przykładzie `"ss"` oznacza, że oba parametry (`$imie` i `$email`) są typu tekstowego (`string`).

3. **Kolejne parametry (`$imie`, `$email`)** – są zmiennymi, które chcesz powiązać z parametrami zapytania SQL. W momencie wykonywania zapytania, te zmienne zostaną użyte w miejscu ich odpowiedników w SQL. Dodatkowo, `mysqli_stmt_bind_param` zapewnia, że dane są automatycznie "oczyszczone" (uniknięcie tzw. SQL Injection), więc są bezpiecznie wprowadzane do bazy danych.

Przykład użycia:

```php
$stmt = mysqli_prepare($conn, "INSERT INTO users (imie, email) VALUES (?, ?)");
mysqli_stmt_bind_param($stmt, "ss", $imie, $email);
mysqli_stmt_execute($stmt);
```

W zapytaniu SQL znak `?` jest zastępowany przez wartości zmiennych `$imie` i `$email`, przekazanych do funkcji `mysqli_stmt_bind_param`.
**Podsumowując**, choć w tym tutorialu skupiliśmy się na prostocie i zrozumiałości kodu, zawsze warto pamiętać o bezpieczeństwie i dobrej praktyce programowania.
