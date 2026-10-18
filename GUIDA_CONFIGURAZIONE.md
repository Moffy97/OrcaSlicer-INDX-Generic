# Guida rapida - OrcaSlicer INDX Generic

Configurazione manuale sulla propria stampante | 17 ottobre 2026

## 1. Scarica e prepara Orca

Questa guida riguarda OrcaSlicer INDX Generic 0.3.0 preview, la versione semplice con configurazione manuale. Non richiede il nostro KlipperScreen e non sincronizza automaticamente tool o filamenti. Non installa firmware o macro sulla stampante.

Apri la release del repository OrcaSlicer-INDX-Generic e, in basso, espandi Assets. Scarica OrcaSlicer-INDX-Generic-Windows-x64.zip, estrai TUTTA la cartella in una posizione scrivibile e avvia Avvia-Orca-INDX.cmd. Non avviare il programma dentro lo ZIP. I dati sono separati da Orca gia installato.

Se usi gia questa versione del programma, puoi importare i soli profili: estrai profili-indx-generici.zip e usa File > Importa > Importa configurazioni per i tre JSON macchina, processo e filamento. Se Orca ufficiale rifiuta un parametro o non offre la gestione multidiametro, usa il programma di questa release.

Seleziona INDX - Generica semplice - DA CONFIGURARE e salvalo con un nome tuo, per esempio Mia stampante INDX - 3 tool. Il volume 200 x 200 x 200 mm e i quattro ugelli da 0,4 mm sono soltanto segnaposto.

Prima di procedere: INDX deve essere installato e collaudato secondo la guida Bondtech per la tua macchina. Un profilo slicer non rende compatibili elettronica, pin, homing o dock. Conserva una copia della configurazione funzionante.

## 2. Numero tool e diametri

Apri le impostazioni della stampante con la matita accanto al profilo. Attiva la visibilita Esperto se un campo non appare. Nella sezione delle capacita/estrusori imposta il numero di estrusori logici uguale ai tool fisici. I nomi delle schede possono variare con lingua e versione.

Configura ogni scheda Extruder 1, Extruder 2, ecc.: diametro ugello realmente montato, limiti di altezza layer e area raggiungibile. La numerazione visuale parte da 1; i comandi della stampante partono da 0.

| Scheda Orca | Tool firmware | Esempio diametro |
| --- | --- | --- |
| Extruder 1 | T0 | 0,4 mm |
| Extruder 2 | T1 | 0,6 mm |
| Extruder 3 | T2 | 0,8 mm |

Esempio: con tre tool, imposta tre estrusori logici, non quattro. Controlla anche che tutti i parametri per estrusore siano coerenti dopo la variazione. Aggiungere un filamento nella barra laterale non aggiunge un dock o un tool alla stampante.

Se la versione espone Standard/CHT per estrusore, scegli il tipo fisico montato. Non aumentare la portata solo in base alla sigla CHT: imposta la portata massima sostenibile nel profilo filamento dopo una misura. La base generica non calcola aumenti automatici.

Mantieni disattivata la modalita estrusore singolo multimateriale: qui ci sono piu tool logici. Gli offset fisici devono essere gestiti dalle macro INDX; lascia a zero quelli XY nello slicer se il firmware li applica gia, per evitare di compensare due volte.

## 3. Macro richieste sulla stampante

Usa il pacchetto ufficiale Bondtech corrispondente al tuo plugin/firmware. Non copiare pin, coordinate o macro di movimento dalla SV08 Max su un altro modello. Le indicazioni seguenti sono un elenco di verifica, non un installer della conversione.

File ufficiali: indx.cfg per la configurazione utente; indx-tc-macros.cfg per CHANGE_TOOL e PARK_TOOL; indx-cal.cfg per la calibrazione; homing.cfg per la sequenza di homing da adattare. Esempio di percorsi, valido soltanto se i file sono davvero nella cartella indx:

```ini
[include indx/indx.cfg]
[include indx/indx-tc-macros.cfg]
[include indx/indx-cal.cfg]
# Includere solo se compatibile con il proprio homing:
# [include indx/homing.cfg]
```

In indx.cfg, TOOL_POSITIONS contiene variable_tool_count e le coordinate variable_tN_x / variable_tN_dock_y per ciascun tool. Il numero e gli indici devono coincidere con Orca. Usa esclusivamente misure reali: nessuna posizione universale puo essere fornita da questo profilo.

Controlla la presenza dei comandi T0...T(N-1), CHANGE_TOOL e PARK_TOOL e delle dipendenze richieste dalla versione ufficiale installata. Per piu tool, estendi le scorciatoie Tn seguendo il modello ufficiale. INDX usa un solo [extruder] fisico: non creare [extruder1], [extruder2] per i tool passivi.

Klipper ammette un solo homing_override: se esiste gia, integra la sequenza con quella della macchina invece di duplicarla. Calibra dock, offset e cella secondo il manuale della versione installata. Non eseguire cambi tool con coordinate di esempio.

## 4. G-code iniziale, cambio e fine

Nelle impostazioni stampante apri Machine G-code / G-code macchina. Il campo Change filament G-code / Cambio filamento del nostro profilo contiene gia:

```ini
CHANGE_TOOL TOOL={next_extruder} TEMP={new_filament_temp}
M400
```

Conserva le parentesi e i nomi esattamente come sono. Orca li sostituisce durante lo slicing con il numero del tool entrante e la sua temperatura. Non aggiungere un secondo cambio tool per la stessa operazione.

Il G-code iniziale generico e volutamente incompleto: conserva questi due marcatori commentati, necessari a questa versione del programma:

```ini
; INDX_BASIC_PROFILE
; INDX_PRINT_START: shared feeder/heater output marker only, NOT a macro call.
```

Sotto i marcatori inserisci la sequenza iniziale gia verificata della TUA macchina: selezione del tool iniziale, homing, temperatura piano, pulizia del nozzle, eventuale livellamento/mesh, riscaldamento e priming nell ordine appropriato. Se usi PRINT_START, i parametri devono essere quelli definiti dalla tua macro. Non esiste un richiamo universale da incollare.

INDX_PRINT_START qui e solo parte di un commento: non serve creare quella macro personalizzata del progetto SV08 Max. Evita anche INDX_PRINT_END e altre macro del progetto completo se non sono presenti sulla tua macchina.

La fine stampa di base spegne ugello, piano e ventola: M104 S0, M140 S0, M107. Non effettua parcheggio del tool o movimenti finali. Se desideri PRINT_END o PARK_TOOL, integra e verifica la tua sequenza: non inserire coordinate generiche.

## 5. Filamenti, slicing e primo controllo

Crea i filamenti reali nella barra laterale e assegna ogni parte o funzione al tool corretto. Se la schermata di stampa propone una mappatura filamento/tool, controllala prima di inviare il file: colore, slot filamento e numero tool non sono sinonimi.

Seleziona il processo INDX - Base semplice 0.20 come punto di partenza e salvalo con un nome chiaro. Per ugelli piu piccoli abbassa altezza layer e larghezze di linea entro i rispettivi limiti. Un ugello 0,2 mm non deve stampare layer alti 0,4 mm. La base non sceglie automaticamente una strategia multidiametro.

Per esempio, in una prova con ugelli da 0,4 e 0,8 mm puoi partire da layer 0,20 mm per entrambi e larghezze compatibili con il tool che esegue ogni funzione. In seguito puoi ottimizzare il riempimento combinato verificandone lo spessore reale in anteprima. Non assumere che cambiare il diametro aggiorni tutti i valori gia forzati nel processo.

Imposta temperatura, portata volumetrica massima, flow e PA del materiale realmente caricato. I valori PLA inclusi sono valori iniziali, non una calibrazione del tuo filamento. Non abbinare materiali diversi senza verificarne adesione, temperature e scopo di utilizzo.

Per la prima prova usa un modello piccolo con due zone assegnate a tool diversi. Dopo Slice verifica tool, layer, larghezza linea e torre di priming nell anteprima; apri il G-code ed esamina i CHANGE_TOOL: indici esistenti, temperature corrette e sequenza iniziale completa.

Il programma e stato verificato con slicing software; la compatibilita fisica della tua macchina resta da collaudare. Solo dopo aver completato il collaudo ufficiale dei dock esegui una prova sorvegliata. Per aggiungere un tool in seguito aggiorna prima la stampante, poi numero, diametro e mappatura in Orca.

## 6. Problemi comuni e download

| Problema | Controllo rapido |
| --- | --- |
| Unknown command CHANGE_TOOL | Macro ufficiali mancanti, non incluse o non compatibili con la versione installata. |
| Tool inesistente | Confronta il numero estrusori Orca, gli indici del G-code e tool_count del firmware. |
| Layer/linea non validi | Rivedi il processo e le impostazioni di ogni estrusore usato. Non basta cambiare il diametro. |
| La stampa non parte bene | Il G-code iniziale del template non fa homing o priming: serve la sequenza della tua macchina. |
| Offset errati | Verifica la calibrazione nel firmware e che lo slicer non applichi una seconda compensazione. |
| Non vedo i download | Apri Releases, seleziona la versione e scorri fino ad Assets. Source code non e il programma Windows. |

Scarica dalla sezione Assets: programma Windows completo; profili separati; questa guida PDF; versione testo della guida; file di verifica delle guide. Lo ZIP programma pubblicato precedentemente resta invariato: questa guida aggiornata si scarica separatamente.

Progetto: https://github.com/Moffy97/OrcaSlicer-INDX-Generic
Release: https://github.com/Moffy97/OrcaSlicer-INDX-Generic/releases/tag/v0.3.1-preview.20261018

Fonti ufficiali consultate il 17 ottobre 2026: Bondtech INDX, sezioni Firmware Configuration e Slicer Setup; file macros/indx.cfg e macros/indx-tc-macros.cfg. Le macro ufficiali evolvono: segui sempre la documentazione che corrisponde alla tua installazione.

https://github.com/BondtechAB/INDX#slicer-setup
https://github.com/BondtechAB/INDX#firmware-configuration
https://github.com/BondtechAB/INDX/blob/main/macros/indx.cfg

Guida del progetto comunitario OrcaSlicer INDX Generic. Non e una release ufficiale OrcaSlicer o Bondtech. La versione completa SV08 Max con integrazioni automatiche resta nel repository OrcaSlicer-INDX.

