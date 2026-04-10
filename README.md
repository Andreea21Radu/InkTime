# InkTime - Smartwatch PCB Design

**Autor:** Radu Andreea
**Licenta:** Apache License 2.0 

## 1. Arhitectura sistemului

Sistemul este construit in jurul microcontroller-ului **nRF52840**, care gestioneaza comunicatia si perifericele.

Structura generala este urmatoarea:




## 2. BOM (Bill of Materials)



## 3. Functionalitate hardware si specificatii tehnice

Proiectul **InkTime** reprezinta un dispozitiv wearable optimizat pentru consum redus de energie, capabil sa functioneze pe perioade lungi de timp datorita combinatiei dintre un microcontroller eficient si un display E-Ink, care consuma energie doar la actualizare.

---

### 3.1 Unitatea centrala (Microcontroller)

In centrul sistemului se afla circuitul **nRF52840**, care integreaza toate functiile principale:

* Procesor **ARM Cortex-M4F la 64 MHz**, suficient pentru procesare de semnale si control periferice
* Suport integrat pentru **Bluetooth Low Energy (BLE)**, utilizat pentru comunicarea cu telefonul
* Moduri de consum redus (sleep si deep sleep), unde curentul scade pana la nivel de microamperi

Acest microcontroller permite rularea simultana a comunicatiei wireless si a interactiunii cu senzorii, mentinand consumul scazut.


### 3.2 Sistem de alimentare

Alimentarea este realizata printr-un lant complet de power management:

* **BQ25180** – responsabil pentru incarcarea bateriei Li-Po prin USB-C, folosind metoda CC/CV si protectii integrate
* **RT6160** – convertor buck-boost care asigura o tensiune stabila de **3.3V**, chiar daca bateria scade sub aceasta valoare
* **MAX17048** – circuit de monitorizare a bateriei, care transmite starea de incarcare catre microcontroller prin I2C

Avantajul folosirii convertorului buck-boost este utilizarea eficienta a bateriei pe intreaga plaja de tensiuni.


### 3.3 Comunicatia intre module

Pentru conectarea componentelor, sistemul foloseste doua interfete principale:

#### Magistrala I2C

Este utilizata pentru perifericele inteligente:

* accelerometrul (IMU)
* fuel gauge-ul
* driverul haptic

Toate aceste componente sunt conectate pe aceeasi magistrala, ceea ce reduce numarul de pini folositi si simplifica traseele pe PCB.

#### Magistrala SPI

Este dedicata exclusiv display-ului:

* display-ul E-Ink este conectat prin SPI folosind conectorul FPC
* aceasta interfata permite transfer rapid de date necesar actualizarii imaginii


### 3.4 Interactiune cu utilizatorul

Dispozitivul include:

* 3 butoane pentru navigare (UP, ENTER, DOWN)
* feedback haptic realizat cu **DRV2605**, controlat prin PWM

Acest sistem permite interactiune intuitiva fara consum mare de energie.


### 3.5 Estimarea consumului de energie

Sistemul este alimentat de o baterie Li-Po de aproximativ **250 mAh**.

Consum estimat:

* Sleep (MCU + BLE): ~35 µA
* Functionare activa (citire senzori): ~120 µA
* Actualizare display: ~25 µA

Curent mediu total:

I_avg ≈ 180 µA = 0.18 mA


### 3.6 Autonomie estimata

Autonomia teoretica:

T = 250 mAh / 0.18 mA ≈ 1380 ore

In conditii reale (BLE, senzori, vibratii):

👉 aproximativ **9 – 14 zile**


## 4. Mapare pini nRF52840

### 4.1 Alocarea pinilor

| Componenta    | Semnal | Pin nRF | Directie | Motiv                |
| ------------- | ------ | ------- | -------- | -------------------- |
| Display E-Ink | SCK    | P0.11   | Output   | Clock SPI            |
| Display E-Ink | MOSI   | P0.12   | Output   | Transfer date        |
| Display E-Ink | CS     | P0.13   | Output   | Selectie dispozitiv  |
| Display E-Ink | DC     | P0.14   | Output   | Data / Command       |
| Display E-Ink | RESET  | P0.15   | Output   | Reset hardware       |
| Display E-Ink | BUSY   | P0.16   | Input    | Status display       |
| I2C           | SDA    | P0.26   | I/O      | Linie date           |
| I2C           | SCL    | P0.27   | Output   | Linie clock          |
| Buton UP      | GPIO   | P1.07   | Input    | Navigare             |
| Buton ENTER   | GPIO   | P1.08   | Input    | Confirmare           |
| Buton DOWN    | GPIO   | P1.09   | Input    | Navigare             |
| Motor haptic  | PWM    | P1.02   | Output   | Control vibratii     |
| RT6160        | EN     | P0.18   | Output   | Activare convertor   |
| IMU           | INT    | P0.25   | Input    | Intrerupere hardware |

---

### 4.2 Decizii de design

* **Port 0** a fost folosit pentru interfete rapide (SPI, I2C)
* **Port 1** a fost folosit pentru butoane, pentru a evita interferente
* pinii SPI au fost grupati fizic pentru trasee mai scurte
* magistrala I2C este comuna pentru mai multe dispozitive → economie de pini
* pinii NFC (P0.09, P0.10) au fost evitati pentru extensii viitoare


## 5. Design log si decizii de implementare

### 5.1 Layout PCB

* componentele au fost plasate compact
* traseele de alimentare au fost facute la **0.3 mm**
* traseele de semnal la **0.15 mm**
* zona antenei a fost lasata fara cupru

### 5.2 Probleme si solutii

* componente 0201 → necesare pentru spatiu redus
* routing dificil sub MCU → s-au folosit trasee mai subtiri local
* antena → zona keep-out pentru performanta RF

### 5.3 Integritate semnal

* condensatori de decuplare (100nF) plasati foarte aproape de pini
* GND realizat cu polygon pour pe ambele straturi
* via stitching pentru masa


## 6. Randari si imagini

(Imaginile se gasesc in folderul `Images`)

* vedere PCB top
* vedere PCB bottom
* randare 3D
* vedere explodata
