# PlayDesign — Konfigurator zakresu wdrożenia

## Cel projektu

Statyczna strona HTML (zero zależności, zero frameworków) prezentująca interaktywny konfigurator zakresu wdrożenia produktu **PlayDesign** — aplikacji do projektowania wnętrz (floorplaner) dla klientów B2B z sektora meblarskiego i retail.

Formularz jest używany podczas spotkań z potencjalnymi klientami (producenci mebli, sklepy meblowe, sieci handlowe). Klient zaznacza interesujące go moduły, a formularz natychmiast pokazuje:
- co musi przygotować po swojej stronie
- co dostarcza PlayDesign
- dodatkowe informacje / uwagi

---

## Produkt PlayDesign — opis modułów

### Zawsze w pakiecie (nie można odznaczyć)
- **Wdrożenie środowiska Web** — konfiguracja i utrzymanie środowiska hostingowego, osadzenie floorplanera na stronie klienta (kod embed / iframe). Stała miesięczna opłata licencyjna.
- **Branding floorplanera** — dostosowanie kolorystyki, logotypu i identyfikacji wizualnej floorplanera do brandingu klienta.
- **API Inspiracji / Publikacji** — PlayDesign udostępnia API z listą opublikowanych projektów (rendery, lista produktów, metadane). Klient konsumuje to API na swojej stronie e-commerce tworząc landing page z inspiracjami. Klient końcowy może klikać "Otwórz we floorplanerze" lub "Zobacz w projekcie" z karty produktu. Klient zarządza publikacją projektów przez panel PlayDesign.

### Katalog produktów — wybór sposobu dostarczenia (radio, jeden z dwóch)
- **Statyczny katalog** — klient dostarcza Excel lub CSV z nazwami, opisami, zdjęciami i modelami 3D. Dane aktualizowane ręcznie. Brak cen i stanów magazynowych.
- **Feed produktowy (API)** — klient udostępnia API (REST / XML / JSON) z cenami i stanami magazynowymi. PlayDesign regularnie zaciąga feed.

### Lista zakupowa — opcje dodatkowe (można zaznaczyć oba)
- **Generowanie PDF** — klient końcowy pobiera PDF z listą produktów z projektu. Dostępne w wersji podstawowej.
- **Integracja koszyka e-commerce** — przycisk "Złóż zamówienie" we floorplanerze wywołuje API klienta i tworzy koszyk. **Zależność: wymaga feedu produktowego** (dane z feedu są przekazywane do zamówienia). Wybranie tej opcji automatycznie przełącza katalog na feed i blokuje możliwość wyboru katalogu statycznego.

### Desktop / VR — opcjonalne
- **Wersja Desktop** — instalacja na stanowiskach w salonach stacjonarnych. Mocniejsza wersja aplikacji (nie webowa). Projekty ze wszystkich wersji trafiają do wspólnej puli organizacji. Dodatkowa opłata miesięczna × liczba stanowisk (pole numeryczne).
- **Tryb VR** — spacer po projekcie w goglach VR ze stanowiska w salonie. **Zależność: wymaga Desktop.** Opcja jest wyszarzona i niedostępna dopóki Desktop nie jest zaznaczony.

---

## Logika zależności

| Akcja | Efekt |
|---|---|
| Zaznaczenie "Koszyk e-commerce" | Automatycznie przełącza katalog na "Feed produktowy" |
| Koszyk aktywny | Opcja "Statyczny katalog" jest zablokowana (disabled) |
| Odznaczenie koszyka | Odblokowuje możliwość wyboru katalogu statycznego |
| Zaznaczenie "Tryb VR" bez Desktop | Niedozwolone — opcja VR jest disabled |
| Odznaczenie Desktop | Automatycznie odznacza VR |

---

## Podsumowanie wymagań (dynamiczne)

Pod konfiguratorem znajduje się sekcja podsumowania podzielona na trzy kategorie oznaczone kolorowym punktem:

- 🔴 **Po stronie klienta** — co klient musi przygotować / dostarczyć
- 🟢 **Dostarcza PlayDesign** — co wchodzi w zakres usługi
- 🔵 **Dodatkowe informacje** — uwagi, info o opłatach, zależności

Podsumowanie aktualizuje się w czasie rzeczywistym przy każdej zmianie konfiguracji.

### Mapowanie opcji → wymagania (zawsze obecne)
- PlayDesign: konfiguracja środowiska, branding, osadzenie embeda, udostępnienie API Inspiracji
- Klient: integracja API Inspiracji na stronie e-commerce, implementacja przycisków "Otwórz we floorplanerze" i "Zobacz w projekcie"
- Info: stała miesięczna opłata licencyjna, klient zarządza publikacją przez panel PlayDesign

### Mapowanie opcji → wymagania (warunkowe)
- Katalog statyczny → Klient: dostarcza Excel/CSV z produktami i modelami 3D; Info: brak cen/stanów, ręczna aktualizacja
- Feed produktowy → Klient: udostępnia API feedu; Info: PlayDesign regularnie synchronizuje
- PDF → PlayDesign: generowanie PDF (bez dodatkowej integracji)
- Koszyk → Klient: udostępnia API koszyka (endpoint przyjmujący listę SKU); PlayDesign: integracja przycisku "Złóż zamówienie"
- Desktop (N stanowisk) → Klient: zapewnienie N stanowisk spełniających wymagania sprzętowe; PlayDesign: instalacja na N stanowiskach; Info: opłata N × stawka, wspólna pula projektów
- VR → Klient: zakup gogli VR kompatybilnych z PlayDesign; PlayDesign: wdrożenie trybu VR

---

## Wymagania techniczne

- **Jeden plik HTML** — bez zewnętrznych JS/CSS, bez npm, bez bundlera
- **Zero zależności runtime** — jedyna zewnętrzna zależność to Google Fonts (opcjonalna, fallback na system-ui)
- **Działa offline** — po pobraniu pliku otwierany dwuklikiem w przeglądarce
- **Responsywny** — czytelny zarówno na laptopie jak i tablecie (spotkania w salonach)
- **Dark mode** — obsługa `prefers-color-scheme: dark` przez CSS variables
- **Przycisk "Drukuj / Zapisz PDF"** — `window.print()`, ukryty w @media print

---

## Design system

### Typografia
- Nagłówki: `DM Serif Display` (Google Fonts)
- Treść: `DM Sans` (Google Fonts), fallback: `system-ui, sans-serif`

### Kolory (light mode)
```
--text-primary: #1a1a1a
--text-secondary: #6b6b6b
--bg: #ffffff
--bg2: #f7f7f5
--border: rgba(0,0,0,0.1)
--border-mid: rgba(0,0,0,0.18)
--teal: #0F6E56
--teal-mid: #1D9E75
--teal-light: #E1F5EE
```

### Typy badge (status sekcji)
- `badge-required` (zielony) — sekcja obowiązkowa
- `badge-included` (niebieski) — zawsze w pakiecie, nie można odznaczyć
- `badge-optional` — do wyboru

### Typy tagów przy opcjach
- `tag-dep` (pomarańczowy) — zależność od innej opcji
- `tag-api-give` (czerwony) — klient musi udostępnić API
- `tag-api-take` (niebieski) — PlayDesign udostępnia API / zawsze w pakiecie

### Stany wierszy opcji
- `.option-row` — domyślny
- `.option-row.selected` — zaznaczony (zielona ramka, zielone tło)
- `.option-row.locked` — zawsze aktywny, nie można odznaczyć (niebieska checkbox, `pointer-events: none`)
- `.option-row.disabled` — niedostępny z powodu zależności (`opacity: 0.38`, `pointer-events: none`)

---

## Deployment

Docelowo: **GitHub Pages** jako statyczna strona z pojedynczego pliku `index.html` w repozytorium.

Sugerowana struktura repozytorium:
```
/
├── index.html        # cały konfigurator
└── CLAUDE.md         # ten plik
```

Włączyć GitHub Pages z brancha `main`, katalog `/` (root).

---

## Możliwe rozszerzenia (nie implementować teraz)

- Krok 2: formularz danych firmy (nazwa, liczba SKU, aktualna platforma e-commerce, liczba salonów)
- Eksport podsumowania do PDF przez dedykowaną bibliotekę (np. jsPDF)
- Wersja wielojęzyczna (PL / EN)
- Integracja z narzędziem CRM do zapisywania konfiguracji klienta
