# ksef-apps-script — faktury KSeF w Google Sheets

Skrypt Google Apps Script, który łączy arkusz Google z serwisem **ZHR KSeF Service**. Co 30 minut pobiera nowe faktury z KSeF, zapisuje ich PDF-y na Dysku Google i pozwala jednym kliknięciem udostępnić fakturę właściwej jednostce lub wydarzeniu.

Poniższa instrukcja jest dla osób nietechnicznych. Całość zajmuje około 20 minut i nie wymaga programowania — tylko kopiowania i wklejania.

## Zanim zaczniesz

Co musisz mieć przygotowane:

| Co | Skąd to wziąć |
| --- | --- |
| Konto Google (najlepiej w domenie ZHR) | Twoje zwykłe konto |
| Klucz API (API key) | Od administratora KSeF ZHR |
| Tenant ID | Od administratora KSeF ZHR |
| Adres serwisu (service URL) — jest już wpisany w skrypcie: `https://ksef.zhr.pl` | Nic nie trzeba robić |
| Folder na Dysku Google (najlepiej na dysku współdzielonym) | Tworzysz go w kroku 1 |

Klucz API działa jak hasło do faktur Twojej jednostki. Nie wysyłaj go mailem dalej, nie wklejaj w komórki arkusza i nie pokazuj na zrzutach ekranu.

## Krok 1: Arkusz i folder na PDF-y

Potrzebujesz jednego pustego arkusza i jednego folderu, do którego trafią PDF-y faktur.

1. Wejdź na [drive.google.com](https://drive.google.com) i otwórz dysk współdzielony swojej jednostki (lub „Mój dysk”).
2. Kliknij **Nowy → Folder** i nazwij go np. `Faktury KSeF`.
3. Otwórz ten folder i spójrz na pasek adresu przeglądarki. Adres wygląda tak:
   - `https://drive.google.com/drive/folders/1AbCdEfGhIjKlMnOpQrStUv`
   - Ciąg znaków po `folders/` (tu: `1AbCdEfGhIjKlMnOpQrStUv`) to **ID folderu**. Skopiuj go do notatnika — przyda się w kroku 3.
4. Wróć do Dysku i kliknij **Nowy → Arkusze Google → Pusty arkusz**.
5. Nazwij arkusz, np. `Faktury KSeF — Hufiec X`.

Arkusza nie musisz niczym wypełniać — skrypt sam utworzy potrzebne zakładki.

## Krok 2: Wklejenie skryptu

Skrypt to gotowy kod z pliku [`Code.gs`](Code.gs) w tym repozytorium. Wklejasz go raz i potem już go nie ruszasz.

1. W arkuszu kliknij w menu **Rozszerzenia → Apps Script**. Otworzy się nowa karta z edytorem.
2. Na górze kliknij „Projekt bez nazwy” i zmień nazwę na `KSeF ZHR`.
3. Po lewej jest plik `Kod.gs` (lub `Code.gs`) z kilkoma linijkami. Zaznacz w nim wszystko (**Ctrl+A**) i usuń.
4. Otwórz [Code.gs na GitHubie](https://github.com/niewesoly/ksef-apps-script/blob/master/Code.gs), kliknij ikonę **Copy raw file** (dwa kwadraciki w prawym górnym rogu kodu) i wklej całość do edytora (**Ctrl+V**). Niczego w kodzie nie zmieniaj.
5. Kliknij ikonę dyskietki (**Zapisz projekt**) lub naciśnij **Ctrl+S**.

Teraz włącz usługę Dysku, która pozwala skryptowi udostępniać PDF-y:

1. W lewym pasku edytora znajdź **Usługi** (Services) i kliknij **+** obok.
2. Na liście wybierz **Drive API**, wersja **v3**, i kliknij **Dodaj**.
3. Po lewej pojawi się pozycja `Drive` — to znak, że się udało.

## Krok 3: Wpisanie danych od administratora

Klucz API, Tenant ID i ID folderu wpisujesz w ukrytych „właściwościach skryptu” — nie w arkuszu, żeby nikt z dostępem do arkusza ich nie zobaczył.

1. W edytorze Apps Script kliknij ikonę koła zębatego **Ustawienia projektu** w lewym pasku.
2. Przewiń na sam dół do sekcji **Właściwości skryptu** i kliknij **Dodaj właściwość skryptu**.
3. Dodaj trzy właściwości. Nazwę przepisz dokładnie tak jak w tabeli (wielkie litery, podkreślniki), a w wartości wklej swoje dane:

   | Właściwość (nazwa) | Wartość |
   | --- | --- |
   | `ZHR_KSEF_API_KEY` | klucz API od administratora |
   | `ZHR_KSEF_TENANT_ID` | Tenant ID od administratora |
   | `ZHR_KSEF_DRIVE_FOLDER_ID` | ID folderu skopiowane w kroku 1 |

4. Kliknij **Zapisz właściwości skryptu**.

Uwaga na spacje: przy kopiowaniu często doklejają się spacje na początku lub końcu. Jeśli później pojawi się błąd 401 lub 403, sprawdź to w pierwszej kolejności.

## Krok 4: Pierwsze uruchomienie

Jednorazowa konfiguracja tworzy zakładki w arkuszu i ustawia automatyczne pobieranie faktur co 30 minut.

1. Wróć do karty z arkuszem i odśwież ją (**F5**). Po kilku sekundach w górnym menu, obok „Pomoc”, pojawi się menu **KSeF**.
2. Kliknij **KSeF → Jednorazowa konfiguracja**.
3. Google poprosi o autoryzację. Kliknij **Kontynuuj** i wybierz swoje konto.
4. Może pojawić się ekran „Google nie zweryfikował tej aplikacji”. To normalne dla własnych skryptów: kliknij **Zaawansowane → Przejdź do KSeF ZHR (niebezpieczne)**.
5. Zaznacz wszystkie zgody i kliknij **Zezwól**. Skrypt potrzebuje dostępu do arkusza, Dysku i połączeń z internetem.
6. Jeśli po autoryzacji nic się nie dzieje, kliknij **KSeF → Jednorazowa konfiguracja** jeszcze raz.
7. Pojawi się okno „Setup zakończony”. W arkuszu są teraz zakładki **Pula**, **Konfiguracja** i **Uprawnienia**.

Sprawdź, czy wszystko jest podłączone:

1. Kliknij **KSeF → Diagnostyka**. Przy „Advanced Drive Service” powinno być **TAK**, a przy `ZHR_KSEF_DRIVE_FOLDER_ID` — **ustawione**.
2. Kliknij **KSeF → Synchronizuj faktury**. Po chwili w prawym dolnym rogu zobaczysz komunikat „X nowych faktur dodanych do Puli” lub „Brak nowych faktur”.
3. Zajrzyj do zakładki **Pula** i do folderu z kroku 1 — powinny tam być faktury i ich PDF-y.

Pierwsza synchronizacja może trwać kilka minut, jeśli faktur jest dużo.

## Krok 5: Codzienna praca

Faktury pobierają się same co 30 minut; Twoja praca to przypisanie każdej faktury do jednostki lub wydarzenia.

Najpierw raz przygotuj listy:

1. W zakładce **Konfiguracja** wpisz w kolumnie A, od wiersza 2, nazwy jednostek lub wydarzeń (np. `Obóz Alfa`, `1 DH Warszawa`). Przykładowy szary wpis możesz nadpisać.
2. W zakładce **Uprawnienia** w każdym wierszu wybierz jednostkę z listy i wpisz jeden adres e-mail osoby, która ma widzieć jej faktury. Kilka osób = kilka wierszy z tą samą jednostką.

Potem, przy każdej nowej fakturze:

1. Otwórz zakładkę **Pula**. Każdy wiersz to jedna faktura: numer, data, sprzedawca, kwota i link **PDF**.
2. W kolumnie **Wydarzenie / Jednostka** wybierz z listy, do kogo należy faktura.
3. Skrypt od razu udostępni PDF wszystkim adresom przypisanym do tej jednostki; dostaną maila od Google. Jeśli udostępnianie nie zadziała, użyj **KSeF → Udostępnij oznaczone faktury**.

Czego nie robić:

- Nie usuwaj ukrytej kolumny **ID (nie usuwać)** — po niej skrypt rozpoznaje, które faktury już pobrał.
- Nie zmieniaj nazw zakładek **Pula**, **Konfiguracja**, **Uprawnienia**.
- Zmiana jednostki na inną nie odbiera dostępu poprzednim osobom — trzeba to zrobić ręcznie na Dysku.

## Gdy coś nie działa

Większość problemów to literówka we właściwościach skryptu albo brak usługi Drive API. Znajdź komunikat w tabeli:

| Komunikat lub objaw | Co zrobić |
| --- | --- |
| „Brak konfiguracji API. Ustaw ZHR_KSEF_API_KEY i ZHR_KSEF_TENANT_ID…” | Wróć do kroku 3 i sprawdź nazwy właściwości litera po literze. |
| „Błąd API: 401” lub „403” | Klucz API lub Tenant ID jest błędny albo ma spację. Wklej ponownie; jeśli nie pomoże, poproś administratora o nowy klucz. |
| „Błąd API: 404” lub błąd połączenia | Sprawdź Tenant ID (krok 3). Jeśli jest poprawny, zgłoś problem administratorowi. |
| „Brak konfiguracji folderu docelowego” | Dodaj właściwość `ZHR_KSEF_DRIVE_FOLDER_ID` (krok 3). |
| „Nie mogę otworzyć folderu o ID…” | ID folderu jest błędne albo nie masz do niego dostępu. Skopiuj je ponownie z paska adresu. |
| „Limit pasma Apps Script wyczerpany” | Nic nie rób. Google ogranicza dzienny transfer; reszta faktur pobierze się sama następnego dnia. |
| W kolumnie PDF jest kreska „—” | PDF nie był jeszcze gotowy w serwisie. Zgłoś numer faktury administratorowi. |
| Wybór jednostki nie udostępnia PDF | Kliknij **KSeF → Zainstaluj trigger onEdit**, potem **KSeF → Diagnostyka**. Sprawdź, czy jednostka ma adresy w **Uprawnieniach**. |
| Brak menu **KSeF** | Odśwież arkusz i odczekaj 10 sekund. Jeśli dalej brak, sprawdź, czy kod w kroku 2 został zapisany. |

## Bezpieczeństwo klucza API

- Edytujący arkusz mogą otworzyć Apps Script i zobaczyć klucz. Dawaj prawo edycji tylko zaufanym osobom; reszcie — tylko podgląd.
- Jeśli klucz wyciekł albo ktoś z dostępem odchodzi z funkcji, napisz do administratora o nowy klucz i podmień go w kroku 3.
- Automatyczna synchronizacja działa z Twojego konta Google. Gdy przekazujesz obowiązki, nowa osoba powinna ponownie wykonać krok 4.

Z pytaniami, których tu nie ma, zwróć się do administratora KSeF ZHR i dołącz zrzut ekranu z **KSeF → Diagnostyka** (nie pokazuje on klucza API).
