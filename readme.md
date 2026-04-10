# Charakterystyka danych

Bank krwi mający filie w kilku miastach rejestruje dane dotyczące donacji.

W strumieniu pojawiają się zdarzenia zgodne ze schematem `DonationEvent`.

```
create json schema DonationEvent(blood_type string, rh string, donation_size int, 
       honorary_donor int, city string, ets string, its string)
```

Każde zdarzenie związane z jest z faktem oddania określonej ilości krwi 
z konkretnej grupy w określonym mieście.

Dane uzupełnione są o dwie etykiety czasowe. 
* Pierwsza (`ets`) związana jest z momentem oddania krwi. 
  Etykieta ta może się losowo spóźniać w stosunku do czasu systemowego 
  maksymalnie do 30 sekund.
* Druga (`its`) związana jest z momentem rejestracji zdarzenia w systemie.

# Opis atrybutów

Atrybuty w każdym zdarzeniu zgodnym ze schematem `DonationEvent` 
mają następujące znaczenie:

* `blood_type` - grupa krwi
* `rh` - grupa krwi rh (+ lub -)
* `city` - nazwa miasta, w którym oddano krew
* `donation_size` - ilość krwi, lub jej składnika, oddana podczas jednej donacji
* `honorary_donor` – czy osoba jest honorowym krwiodawcą (1-tak, 0-nie)
* `ets` - czas donacji
* `its` - czas rejestracji faktu oddania krwi w systemie

# Zadania
Opracuj rozwiązania poniższych zadań. 
* Opieraj się strumieniu zdarzeń zgodnych ze schematem `DonationEvent`
* W każdym rozwiązaniu możesz skorzystać z jednego lub kilku poleceń EPL.
* Ostatnie polecenie będące ostatecznym rozwiązaniem zadania musi 
  * być poleceniem `select` 
  * posiadającym etykietę `answer`, przykładowo:
  ```aidl
    @name('answer') SELECT blood_type, rh, 
         sum(donation_size) as donation_sum, 
         count(*) howMany, ets, its
    from DonationEvent#ext_timed(java.sql.Timestamp.valueOf(its).getTime(), 3 sec)
  ```

## Zadanie 1
Utrzymuj informacje dotyczące tego, jaka jest łączna ilość krwi z każdej grupy, 
sumując dane od początku działania systemu.

Wyniki powinny zawierać, następujące kolumny:
- `blood_type` - grupę krwi 
- `donation_sum` - łączna ilość krwi z danej grupy.

## Zadanie 2
Wykrywaj przypadki, kiedy oddano mniej niż 300 ml krwi.

Wyniki powinny zawierać, następujące kolumny:
* `blood_type` - grupa krwi
* `rh` - grupa krwi rh (+ lub -)
* `city` - nazwa miasta, w którym oddano krew
* `donation_size` - ilość krwi, lub jej składnika, oddana podczas jednej donacji


## Zadanie 3
Znajduj przypadki, w których wielkość donacji jest równa 
maksimum z donacji zarejestrowanych w ciągu ostatnich 10 sekund.

Wyniki powinny zawierać, następujące kolumny:
* `blood_type` - grupa krwi
* `rh` - grupa krwi rh (+ lub -)
* `city` - nazwa miasta, w którym oddano krew
* `donation_size` - ilość krwi, lub jej składnika, oddana podczas jednej donacji


## Zadanie 4
Utrzymywane są 2 listy. W pierwszej utrzymywane są miasta, dla których 
oddano najwięcej ilości krwi grupy A w ciągu ostatniej minuty. 
Na drugiej liście utrzymywane są miasta, dla których oddano 
najwięcej ilości krwi grupy B w ciągu ostatniej minuty.
Znajduj te miasta, które w ciągu ostatniej minuty znalazły się 
na obu tych listach jednocześnie.

Wyniki powinny zawierać, następujące kolumny:
* `city` - nazwa miasta
* `blood_typeA` - ilość krwi grupy A oddana w ciągu ostatniej minuty
* `blood_typeB` - ilość krwi grupy B oddana w ciągu ostatniej minuty

## Zadanie 5
Postawiono hipotezę, że osoba, która spotkała honorowego krwiodawcę, 
sama ma większe szanse na zostanie honorowym krwiodawcą.
Żeby to sprawdzić, chcemy znajdować osoby, które oddawały krew przed 
honorowym krwiodawcą w tym samym mieście.

Wyszukuj serie co najmniej dwóch donacji osób, które nie są honorowymi krwiodawcami, 
które są zakończone donacją dokonaną przez honorowego krwiodawcę.
Wszystkie donacje muszą być zarejestrowane w okresie nie dłuższym niż jedna minuta.
Wszystkie donacje w serii muszą pochodzić z tego samego miasta
co donacja honorowego krwiodawcy kończąca serię.

Wyniki powinny zawierać, następujące kolumny:
* `start_its` - czas rozpoczęcia serii,
* `stop_its` - czas zakończenia serii (donacja honorowego krwiodawcy),
* `city` - nazwa miasta.

## Zadanie 6
Jest okres wakacyjny i mamy bardzo duże zapotrzebowanie na krew. 
Stanowiska do pobierania krwi działają pełną parą. 
Żeby zoptymalizować naszą pracę, chcemy promować dawców z grupą krwi 0, 
ponieważ ich krew można przetoczyć każdemu. 
Następnie chcemy odbierać krew od dawców grupy krwi B lub A, a na koniec AB, 
ponieważ jest najmniej uniwersalna.

Wykrywaj serie donacji (następujących po sobie bezpośrednio), 
w których najpierw występuje grupa krwi 0, 
następnie A lub B a na koniec AB.

Wyniki powinny zawierać, następujące kolumny:
* `blood_type_0` - grupa krwi, która wystąpiła jako pierwsza w sekwencji
* `blood_type_1` - grupa krwi, która wystąpiła jako druga w sekwencji
* `blood_type_2` - grupa krwi, która wystąpiła jako trzecia w sekwencji

## Zadanie 7
Wykrywaj serie co najmniej trzech donacji w tym samym mieście,
w których każda kolejna donacja jest mniejsza niż poprzednia.

Wyniki powinny zawierać następujące kolumny:
* `city` - nazwa miasta
* `first_donation` - wielkość pierwszej donacji w serii
* `last_donation` - wielkość ostatniej donacji w serii
* `how_many` - liczba donacji w serii
