# HeartGame op ZYNQ-7020

**Auteurs:** Milan Posman ([milan.posman@student.pxl.be](mailto:milan.posman@student.pxl.be)) & Jonas Vanmarsenille ([jonas.vanmarsenille@student.pxl.be](mailto:jonas.vanmarsenille@student.pxl.be))  
**Platform:** PYNQ-Z2 (Xilinx Zynq-7020)  

---

## 📖 Abstract
**HeartGame** is een interactief spel voor twee spelers waarin reactiesnelheid en communicatie centraal staan:
* **Speler 1 (Verdediger):** Bestuurt een hart en kan de kleur van het hart veranderen.
* **Speler 2 (Aanvaller):** Vuurt gekleurde projectielen af op het hart.

Om schade te voorkomen, moet de speler die het hart bestuurt exact de kleur van het inkomende projectiel matchen voordat het inslaat. 

De spelers communiceren via **UDP** met een **PYNQ-board** (Zynq-7020 SoC). De spelverwerking gebeurt in real-time met **FreeRTOS** op het Processing System (PS), waarna de spelwereld en status via **HDMI** op een monitor worden weergegeven via de Programmable Logic (PL).

---

## 🎯 Probleemstelling & Onderzoeksvragen

### Probleemstelling
Het systeem moet input van twee spelers simultaan en met minimale latency verwerken. Een naadloze samenwerking tussen netwerkcommunicatie (UDP), real-time logica (FreeRTOS) en videorendering (HDMI) is noodzakelijk voor een vloeiende spelervaring.

### Hoofdonderzoeksvraag
> *Hoe kan een real-time tweespelerspel op een Zynq-7020 worden gerealiseerd waarbij de input van beide spelers via UDP wordt ontvangen, met FreeRTOS wordt verwerkt en de spelstatus via HDMI wordt weergegeven?*

### Deelvragen
1. Hoe wordt de input van beide spelers betrouwbaar en snel via UDP verwerkt?
2. Hoe worden de spelregels en -logica efficiënt uitgevoerd binnen FreeRTOS tasks?
3. Hoe wordt de grafische spelstatus via de Programmable Logic naar de HDMI-uitgang getransporteerd?

---

## 🏗️ Systeemarchitectuur & Hardware

```
+-------------------+      UDP 1 (Poort 5001)
| Speler 1 - Hart   | --------------------+
| (3 drukknoppen)   |                     |
+-------------------+                     v
                                  +-------------------+       +-------------------+
                                  |   Ethernet Poort  | ----> | PYNQ-Board        | ----> HDMI ----> [ Monitor ]
                                  +-------------------+       | (Zynq-7020 SoC)   |
+-------------------+                     ^                   +-------------------+
| Speler 2 - Project| --------------------+
| (3 drukknoppen)   |      UDP 2 (Poort 5002)
+-------------------+
```

### Hardware Components
* **Processing Board:** PYNQ-board (Xilinx Zynq-7020)
* **Inputs:** 2x Controllers met elk 3 drukknoppen (booleans)
* **Outputs:** HDMI Monitor (Spelbeeld, harten, projectielen, score en levens)

### Software & Hardware Stack
* **Processing System (PS - ARM Cortex-A9):**
  * **FreeRTOS Tasks:** UDP Server 1 (poort 5001), UDP Server 2 (poort 5002), Game Logic / Beeldopbouw, Fouthandeling.
  * **Applicatielaag:** Inputverwerking, vergelijken van kleuren, updaten spelstatus.
* **Programmable Logic (PL - FPGA):**
  * **Video Pipeline:** Framebuffer in DDR geheugen, timing/synchronisatie, pixel formatter.
  * **HDMI Tx Subsystem:** HDMI Encoder, oversampling, AXI4-Stream Video Out.
* **Interconnect:** AXI-interfaces voor PS-PL communicatie.

---

## 🎮 Spelregels & Functionaliteit

1. **Besturing:**
   * **Speler 1:** Kiest uit 3 kleuren voor het hart.
   * **Speler 2:** Kiest uit 3 kleuren voor het af te vuren projectiel.
2. **Mechanica:**
   * Projectielen bewegen vanaf de rechterzijde richting het hart.
   * Als de kleur van het hart **overeenkomt** met het projectiel bij inslag: geen schade.
   * Als de kleuren **niet overeenkomen**: het hart verliest een leven (HP).

---

## 🚀 Toekomstige Uitbreidingen
* **Meerdere Kleuren:** Uitbreiden van de kleurenpaletten voor zowel hart als projectielen.
* **Dynamische Snelheid:** Projectielsnelheid geleidelijk verhogen naarmate de score stijgt.
* **Power-ups / Speciale Projectielen:** Extra variatie om de gameplay uitdagender en interessanter te maken.

---

## 📚 Referenties
* [KISS Principle - Wikipedia](https://en.wikipedia.org/wiki/KISS_principle)
* [SMART Criteria - Wikipedia](https://en.wikipedia.org/wiki/SMART_criteria)
* [YouTube - Vivado / PYNQ Reference Video](https://www.youtube.com/watch?v=TN0EOhoow28)