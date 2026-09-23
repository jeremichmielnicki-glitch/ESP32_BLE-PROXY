# ESP32 BLE-Proxy, Czujnik Środowiskowy i Obecności dla Home Assistant

Projekt wielofunkcyjnego urządzenia smart home opartego na mikrokontrolerze ESP32-C3, który pełni funkcję Bluetooth Proxy dla systemu Home Assistant, a jednocześnie monitoruje parametry atmosferyczne oraz obecność domowników.

## Działanie

Projekt opiera się na mikrokontrolerze ESP32-C3 Super Mini, łączącym się z systemem Home Assistant za pomocą Wi-Fi. Urządzenie odczytuje dane z podłączonych sensorów i przesyła je jako natywne encje HA.

*   **Detekcja obecności (mmWave):** Zastosowany mikrofalowy czujnik obecności pozwala na wykrycie człowieka nawet w pełnym bezruchu (reaguje na mikroruchy, takie jak unoszenie się klatki piersiowej podczas oddechu). Sensor komunikuje się z mikrokontrolerem przez interfejs UART i dostarcza szczegółowe dane, m.in. odległość od obiektu, siłę sygnału, podział na strefy oraz status ruchu/bezruchu.
*   **Pomiary atmosferyczne (I2C):** Moduł łączący czujniki AHT20 i BMP280 odpowiada za monitorowanie środowiska. AHT20 mierzy wilgotność względną oraz temperaturę (z wysoką dokładnością do 0,3°C), natomiast BMP280 odpowiada za pomiar ciśnienia atmosferycznego. Ze względu na wyższą precyzję, docelowa temperatura w systemie odczytywana jest z czujnika AHT20.
*   **Sygnalizacja LED:** Dioda RGB ze wspólną katodą podłączoną do masy jest sterowana bezpośrednio z trzech pinów GPIO, co pozwala na wizualną prezentację stanu urządzenia lub alertów z Home Assistant.
*   **Zasilanie:** Cały układ zasilany jest standardowo poprzez wbudowany port USB-C.

## Lista elementów

| Element | Model | Interfejs / Uwagi |
| :--- | :--- | :--- |
| **Mikrokontroler** | ESP32-C3 Super Mini | Wi-Fi + BLE (Bluetooth Proxy) |
| **Czujnik atmosferyczny** | AHT20 + BMP280 | I2C |
| **Czujnik obecności** | HLK-LD2410C | UART (czujnik radarowy 24 GHz) |
| **Dioda sygnalizacyjna** | Dioda RGB 5 mm | Wspólna katoda |

## Zawartość repozytorium

*   **Program:** Kod mikrokontrolera znajduje się w pliku `ESP32-BLE-PROXY.yaml`. Został przygotowany i wgrany za pomocą dodatku ESPHome Device Builder w Home Assistant.
*   **Zdjęcia:** Etapy budowy projektu, schemat oraz gotowe urządzenie znajdują się w folderze `pictures`.

## Uwagi i wnioski z realizacji

*   **Temperatura procesora:** Ze względu na jednoczesną, intensywną pracę modułu Wi-Fi oraz Bluetooth, mikrokontroler wyraźnie się nagrzewa. Po wielogodzinnej pracy układ utrzymuje stabilną temperaturę na poziomie około 65°C. Dla bezpieczeństwa wartość ta jest wysyłana w czasie rzeczywistym do Home Assistant. W przypadku przekroczenia zdefiniowanego progu system generuje powiadomienie push (mechanizm ten zrealizowałem po stronie automatyzacji HA, odciążając sam mikrokontroler).
*   **Wpływ ciepła na odczyty (Kalibracja):** Oba moduły atmosferyczne wykazywały zawyżoną temperaturę. Wynika to z przewodnictwa cieplnego od rozgrzanego procesora ESP32 przez łączące je przewody oraz małą przestrzeń wewnątrz obudowy. Po osiągnięciu stabilizacji termicznej przez mikrokontroler, odczyt temperatury otoczenia był stale zawyżony o ok. 7°C. W docelowej konfiguracji zastosowałem korektę przesunięcia (offset) zmniejszającą wartość encji. Korekta ta została celowo pominięta w udostępnionym kodzie na GitHubie, ponieważ jej wartość zależy od finalnych warunków otoczenia oraz rodzaju zastosowanej obudowy.
