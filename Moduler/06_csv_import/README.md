# 🧩 Modul 06 – CSV og BULK INSERT til MSSQL

## 🎯 Læringsmål
Efter dette modul skal I kunne:
- Eksportere og formatere CSV-filer korrekt fra Unified Runtime (Comfort Panel eller PC Runtime)
- Opsætte SQL Server til at modtage og integrere CSV-data via `BULK INSERT`, PowerShell eller SSIS
- Forstå fordele, begrænsninger og fejlkilder ved filbaseret dataudveksling
- Argumentere for, hvornår CSV-baseret integration er den mest hensigtsmæssige løsning i industrien

## 🧠 Forudsætninger
For at kunne gennemføre øvelserne i dette modul skal I:
- Have adgang til et Unified-system med skriveadgang til lokalt filsystem eller eksternt medie (USB, SD, net share)
- Have Microsoft SQL Server installeret og konfigureret til at tillade `BULK INSERT`
- Være fortrolige med grundlæggende SQL, TIA Portal-scripting (JavaScript i Unified), og have kendskab til Windows-sti-format og administratorrettigheder
- Have installeret evt. værktøjer såsom SSMS, PowerShell og SQL Server Integration Services (valgfrit)

## ⚙️ Systemarkitektur og datastrøm
```
[Unified Runtime] -- (CSV-fil genereres lokalt) --> [Del mappe / Net share / USB] -- (Importer via script eller job) --> [MSSQL tabel]
```
Typiske eksportplaceringer:
- Comfort Panel: `/usb/export.csv`, `/sdcard/export.csv`
- Unified PC Runtime: `C:\data\csv\sensorlog.csv`, `\NAS\share\mssqlimport.csv`

## 🔧 Trinvis konfiguration af CSV-import

### A. Unified Runtime (skriv CSV-fil via script)
Eksempel: skriv en ny fil med header + en enkelt måling:
```javascript
let header = "Timestamp,TagName,TagValue\n";
let row = `${Unified.Time.Now()},TempSensor1,${Tag_TempSensor1}\n`;
fs.writeText("/usb/export.csv", header + row);
```
Hvis filen allerede findes og du vil logge løbende:
```javascript
let row = `${Unified.Time.Now()},TempSensor1,${Tag_TempSensor1}\n`;
fs.appendText("/usb/export.csv", row);
```
> 💡 Husk at CSV-formatet skal matche destinationstabel præcist – inkl. datatype og rækkefølge.

### B. Import til MSSQL

#### Mulighed 1: SQL BULK INSERT
```sql
BULK INSERT WorkshopDB.dbo.TagLog
FROM 'D:\Unified\csv\export.csv'
WITH (
    FIRSTROW = 2,
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    TABLOCK
);
```
> SQL Server-tjenesten skal have læserettigheder til stien – del evt. mappen med "Everyone" (kun til test).
> Sørg for at søgesti er lokal ift. SQL Server – ikke klientens filsti.

#### Mulighed 2: PowerShell automatisering
```powershell
Invoke-Sqlcmd -ServerInstance "localhost" -Database "WorkshopDB" -Query "
BULK INSERT TagLog FROM 'D:\Unified\csv\export.csv'
WITH (FIELDTERMINATOR = ',', ROWTERMINATOR = '\n')"
```
- Planlæg kørslen i **Windows Task Scheduler** til fx hvert 5. minut
- Log evt. output og fejl med `Try/Catch`-blok og `Out-File`

#### Mulighed 3: SSIS (SQL Server Integration Services)
- Opret et "Flat File Source" → "OLE DB Destination" flow
- Map kolonnerne korrekt og håndtér fejl i separate output
- Kør via SQL Agent Job eller SSIS GUI
> SSIS giver ekstra værdi ved transformationer, validering og logik (fx hvis CSV-indholdet skal renses eller beriges)

## 📂 Aktivitet
1. Programmer Unified til at eksportere CSV med mindst ét målepunkt
2. Indlæs filen til MSSQL via én af de tre metoder
3. Verificér import og dataintegritet (korrekt antal rækker, tidspunkter og værdier)
4. Dokumentér evt. fejlscenarier (forkert sti, manglende fil, forkert format)

## 📌 Output til portfolio
- CSV-fil eksempel (1-3 rækker) med korrekt format
- Skærmbillede fra SQL Server, hvor data er blevet importeret
- SQL/BULK INSERT-kommando, PowerShell-script eller SSIS-flow
- Egen refleksion over metoden og evt. forbedringsforslag (filnavngivning, fejlhåndtering, backup)

## 🔍 Refleksionsspørgsmål
- Hvad gør CSV-import mere robust eller svagere sammenlignet med ODBC- eller OPC UA-baseret datalogning?
- Hvordan håndterer man “missed writes” i tilfælde af filkorruption eller netværksfejl?
- Hvad er fordelene ved at navngive CSV-filer unikt (timestamp i filnavn)?
- Hvordan vil du implementere rollback eller dublet-check, hvis data importeres to gange?

---

I næste modul bevæger vi os væk fra filbaseret kommunikation og ind i **publish/subscribe-verdenen**, hvor I skal lære at bruge **MQTT som datakanal** mellem Unified og SQL Server via Telegraf og en MQTT-broker.

