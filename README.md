# OrcaSlicer INDX Generic — base semplice

Versione sperimentale per stampanti con Klipper/Kalico e macro Bondtech INDX gia installate. Il profilo generico richiede adattamento: non e una compatibilita universale gia verificata. Nessuna stampa fisica eseguita.

## Download

- [OrcaSlicer INDX Generic — Windows x64 ZIP](https://github.com/Moffy97/OrcaSlicer-INDX-Generic/releases/download/v0.3.0-preview.20261017/OrcaSlicer-INDX-Generic-Windows-x64.zip)
- [Soli profili generici](profili-indx-generici.zip)
- [Tutte le release](https://github.com/Moffy97/OrcaSlicer-INDX-Generic/releases)

Estrarre completamente lo ZIP in una cartella scrivibile e aprire **Avvia-Orca-INDX.cmd**. La cartella dati e separata da quella di Orca gia installato; avvio con piatto vuoto. Nessun indirizzo stampante, filamento personale o calibrazione incluso.

## Cosa include

Profilo macchina con quattro tool iniziali da 0,4 mm, processo manuale da 0,20 mm e PLA standard da calibrare. Numero di tool e diametri si modificano nelle impostazioni stampante native. Il cambio tool usa il comando Bondtech `CHANGE_TOOL TOOL={next_extruder} TEMP={new_filament_temp}` seguito da `M400`. L'output tratta feeder e riscaldatore INDX come un'unica unita fisica.

**Nessuna sincronizzazione automatica, libreria touch personalizzata o Auto slicing optimization INDX. Nessun plugin KlipperScreen richiesto.** La base software e condivisa con il progetto SV08 Max, ma il marcatore `INDX_BASIC_PROFILE` disabilita quei servizi per questo profilo. Il marcatore `INDX_PRINT_START` compare solo in un commento per attivare l'output del feeder condiviso: non viene eseguita quella macro personalizzata.

## Prima di usare il profilo

1. Sostituire il volume **200 x 200 x 200 mm di esempio**, le aree raggiungibili da ogni tool e i limiti di velocita/accelerazione con quelli reali della propria stampante.
2. Impostare numero dei tool, diametri, offset gestiti dal firmware e gli abbinamenti filamento/tool.
3. Copiare nel G-code iniziale la sequenza di avvio, homing, pulizia e calibrazione gia verificata per la propria macchina. Il template non contiene una sequenza di avvio universale.
4. Verificare le macro ufficiali Bondtech, i dock e gli offset. Il profilo non installa firmware, pinout o configurazioni di movimento.
5. Impostare i filamenti reali e controllare anteprima e G-code prima del collaudo.

Per un'installazione Orca esistente: File > Importa > Importa configurazioni e selezionare i tre JSON estratti da `profili-indx-generici.zip`. Le impostazioni di partenza non sono calibrazioni.

## Verifica e sorgente

I profili standalone sono stati caricati dal programma e usati per generare il G-code di un cubo da 10 mm senza GUI, con le macro di cambio tool attese. La prova verifica il software, non il montaggio fisico.

`UPSTREAM.json` identifica il sorgente Orca ufficiale esatto; `OrcaSlicer-INDX.patch` contiene tutte le modifiche corrispondenti al binario condiviso. `python prepare_source.py` ricostruisce il sorgente in una cartella nuova. Compilare con toolchain e dipendenze Orca ufficiali. Licenza AGPL-3.0, mantenuta nello ZIP.

La versione completa per [SV08 Max e KlipperScreen](https://github.com/Moffy97/OrcaSlicer-INDX) resta in un repository separato. [Istruzioni ufficiali Bondtech INDX](https://github.com/BondtechAB/INDX#slicer-setup). Questo progetto non e una release ufficiale OrcaSlicer o Bondtech.
