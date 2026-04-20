README  
  
┌─────────────────────────────────────────────────────────────┐  
│                        POWER SUPPLY                         │  
│                                                             │  
│  Li-Po Battery (3.7V)                                       │  
│       │                                                     │  
│       ▼                                                     │  
│  BQ25180 (Charger IC)  ◄──── USB Type-C (J4)                │  
│       │                                                     │  
│       ▼                                                     │  
│  RT6160A (Buck-Boost)  ──► 3.3V stable output               │  
│       │                                                     │  
│  MAX17048 (Fuel Gauge) ──► I2C ──► nRF52840                 │  
└─────────────────────────────────────────────────────────────┘  
  
┌─────────────────────────────────────────────────────────────┐  
│                    PROCESSING UNIT                          │  
│                                                             │  
│              nRF52840 (ARM Cortex-M4F)                      │  
│        ┌──────────────┴──────────────┐                      │  
│        │                             │                      │  
│      SPI bus                       I2C bus                  │  
│        │                             │                      │  
│  e-Paper Display               ┌─────┴──────┐               │  
│  (1.54" GDEW0154)              │            │               │  
│                            BMA421       DRV2605YZFR         │  
│                           (Accel.)    (Haptic Driver)       │  
└─────────────────────────────────────────────────────────────┘  
  
  
  
Descrierea Funcționalității Hardware  Sistemul InkTime v6 este optimizat pentru autonomie maximă, utilizând componente de ultimă generație.  
## Microcontroller — nRF52840  
Utilizăm SoC-ul **nRF52840** (U1) care integrează un nucleu ARM Cortex-M4F la 64 MHz. Acesta gestionează stiva **Bluetooth Low Energy (BLE)** și coordonează perifericele prin interfețele SPI și I2C.  
  
Sistemul de Alimentare (Power Tree)  
**BQ25180 (IC1):** IC de încărcare liniară cu managementul căii de alimentare (Power Path).  
**MAX17048 (U3):** Fuel Gauge care utilizează algoritmul ModelGauge pentru monitorizarea precisă a stării de încărcare a bateriei via I2C, fără a necesita calibrare manuală.  
**RT6160A (IC9):** Regulator Buck-Boost care menține o ieșire stabilă de 3.3V pe tot parcursul ciclului de descărcare al bateriei (4.2V - 3.0V).  
  
## Afișaj e-Paper (1.54")  
Utilizăm panoul **GDEW0154** conectat prin J1. Consumul este zero între actualizări. Schema include un **E-Paper Drive Circuit** cu inductorul **L5 (5.6uH)** și diode Schottky **MBR0530** (D2, D4, D5) pentru generarea tensiunilor de refresh. Alimentarea este controlată de un MOSFET P-Channel (Q1) pentru a elimina curentul de scurgere în standby.  
  
Senzor de Mișcare și Haptics  
**BMA421 (IC3):** Accelerometru triaxial ultra-low-power utilizat pentru funcția de *tilt-to-wake* și pedometru.  
**DRV2605YZFR (IC2):** Driver haptic specializat care controlează motorul de vibrații, oferind peste 100 de efecte tactile pre-programate via I2C.  
  
## Configurația Pinilor nRF52840  
Maparea pinilor a fost optimizată pentru rutarea pe 4 straturi a PCB-ului:  
+1  
  

| Semnal   | Pin nRF52 | Direcție      | Descriere                  |
| -------- | --------- | ------------- | -------------------------- |
| EPD_SCK  | P0.28     | Output        | Clock SPI pentru afișaj    |
| EPD_MOSI | P0.29     | Output        | Date imagine către afișaj  |
| EPD_CS   | P0.30     | Output        | Chip Select Display        |
| EPD_DC   | P0.31     | Output        | Data/Command selector      |
| I2C_SDA  | P0.26     | Bidirecțional | Magistrală date senzori    |
| I2C_SCL  | P0.27     | Output        | Magistrală clock senzori   |
| EPD_PWR  | P0.13     | Output        | Control alimentare E-Paper |
| SW_UP    | P1.00     | Input         | Buton Navigare Sus         |
| SW_ENT   | P1.01     | Input         | Buton Selecție             |
| SW_DN    | P1.02     | Input         | Buton Navigare Jos         |
  
  
## Calcule Consum Energie (Estimativ)  
* **Deep Sleep (System ON):** ~3.2 μA.  
* **I2C Polling (Senzori):** ~150 μA.  
* **E-Paper Refresh:** ~8 mA (timp de 2 secunde).  
* **Baterie 250 mAh:** Autonomie estimată de **35 zile** (cu 10 refresh-uri/zi și Bluetooth activ).  
## Integrare Mecanică  
Designul a fost validat în Fusion 360 prin analiză de coliziune între baterie și componentele înalte (inductorul L5 și conectorul USB-C). Carcasa WearAwareNewCase oferă o protecție IP67 simbolică.  
