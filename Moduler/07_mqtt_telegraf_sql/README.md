# 🧩 Modul 07 – MQTT → Telegraf → MSSQL

## 🎯 Læringsmål
Efter dette modul skal I kunne:
- Forstå, hvordan MQTT fungerer som en publish/subscribe-baseret kommunikationsprotokol
- Forklare forskellen mellem MQTT og tidligere pull-baserede løsninger som CSV og ODBC
- Konfigurere Unified Runtime til at sende måledata til en MQTT-broker
- Installere og konfigurere Telegraf til at abonnere på MQTT-data og videresende dem til Microsoft SQL Server
- Evaluere fordele og ulemper ved at anvende MQTT og Telegraf i industrielt dataintegrationsarbejde
  
## 🧠 Forudsætninger – Teknisk overblik og detaljeret installationsvejledning (Windows og Linux)

Inden I går i gang med dette modul, er det helt centralt, at både systemkomponenter, værktøjer og netværksforbindelser er korrekt sat op. Dette afsnit beskriver alle forudsætninger og installationsvejledninger, der sikrer en velfungerende MQTT → Telegraf → MSSQL integration. Hvert element er uddybet med både formål, teknisk kontekst og praktiske kommandoer – til både Windows og Linux.

---

### 1. MQTT-broker – Installation, opsætning og validering
**Formål:** En MQTT-broker fungerer som centralt bindeled mellem datakilder (publishers) og datamodtagere (subscribers). Den anvendes her som middleware mellem Unified Runtime og Telegraf.

**Installation på Windows (Mosquitto):**
1. Download installationspakken fra: https://mosquitto.org/download/
2. Kør `.exe`-installeren, og vælg at installere som Windows Service
3. Tilføj følgende til `mosquitto.conf` (fx under `C:\Program Files\mosquitto`):
```conf
listener 1883
allow_anonymous true
```
4. Start Mosquitto via Services eller med:
```powershell
Start-Service mosquitto
```
5. Verificér med `mosquitto_sub.exe` (findes i klientpakken):
```powershell
mosquitto_sub -t unified/data -v
```

> 💡 Installer Visual C++ redistributables, hvis du får fejl ved opstart.

**Installation på Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install mosquitto mosquitto-clients
```

**Redigér konfiguration:**
```conf
default_listener 1883
allow_anonymous true
```

**Start tjeneste:**
```bash
sudo systemctl enable mosquitto
sudo systemctl start mosquitto
```

---

### 2. Unified Runtime – Opsætning som MQTT-publisher
**Formål:** Unified Runtime fungerer som sensorinterface og datakilde, og sender målinger som JSON-payloads til en MQTT-topic.

**Forudsætninger:**
- Systemet skal være et Siemens Comfort Panel eller Unified PC Runtime med JavaScript-aktiveret scripting
- Runtime-enheden skal kunne nå brokerens IP og port 1883 uden firewallblokering

**Eksempel på JSON-format og publish-kald:**
```javascript
let payload = `{"timestamp":"${Unified.Time.Now()}","tag":"TempSensor1","value":${Tag_TempSensor1}}`;
MqttClient.Publish("unified/data", payload);
```
> Sørg for, at `MqttClient` er korrekt initialiseret. Der kan konfigureres med username/password, QoS og retain-flag.

Verificér output med MQTT-klient (fx `mosquitto_sub`) fra en anden maskine.

---

### 3. Telegraf – Installation, konfiguration og kobling til SQL Server
**Formål:** Telegraf agerer som dataopsamler og sender. Den abonnerer på MQTT-topic’en og skriver værdier videre til en SQL Server-instans.

**Installation på Windows:**
1. Download fra: https://portal.influxdata.com/downloads/
2. Pak ud til f.eks. `C:\Telegraf`
3. Redigér `telegraf.conf`
4. Kør Telegraf med:
```powershell
cd C:\Telegraf
telegraf.exe --config telegraf.conf
```
> Du kan også oprette Telegraf som Windows Service med NSSM (Non-Sucking Service Manager) for automatisk opstart.

**Eksempel på konfiguration:**
```toml
[[inputs.mqtt_consumer]]
  servers = ["tcp://localhost:1883"]
  topics = ["unified/data"]
  data_format = "json"
  tag_keys = ["tag"]
  json_string_fields = ["timestamp"]

[[outputs.sqlserver]]
  server = "Server=localhost;Port=1433;Database=WorkshopDB;User Id=unified;Password=demo123;"
  table = "TagLog"
  include_field = ["value"]
  fieldpass = ["value"]
  tagpass = ["tag"]
```

> 💡 Telegraf til Windows kræver ODBC-driver og en fungerende SQL Server på port 1433. Brug SQL Server Configuration Manager til at aktivere TCP/IP.

**Fejlsøgning:**
- Tjek `telegraf.log`
- Brug `telegraf.exe --test` for at debugge inputs

---

### 4. SQL Server – Strukturkrav til databasen
**Formål:** SQL Server fungerer som mål for al opsamlet data. Strukturen i databasen skal være i overensstemmelse med det dataformat, Unified sender.

**Eksempel på passende tabeldefinition:**
```sql
CREATE TABLE TagLog (
  timestamp DATETIME,
  tag NVARCHAR(100),
  value FLOAT
);
```
> Det er vigtigt, at kolonnenavne matcher felterne i JSON-payloaden, og at datatyperne stemmer overens med det Telegraf sender.

---

### 5. Netværks- og formatforståelse
For at kunne implementere og fejlsøge løsningen korrekt, forventes det, at I har grundlæggende viden om:
- Hvordan MQTT-topics navngives og organiseres hierarkisk (fx `unified/data`, `unified/status`)
- Hvad Quality of Service (QoS) betyder i MQTT (QoS 0, 1, 2)
- Hvilke TCP-porte der skal åbnes mellem Unified, broker og Telegraf (typisk 1883 internt og evt. 8883 til TLS)
- Hvordan JSON-struktur læses og valideres – fx med `jq`, online JSON-validators eller debug i Node-RED/Telegraf
- Hvordan man inspicerer logfiler og statusoutput fra Telegraf og Mosquitto

---

## 📡 Systemarkitektur og datastrøm
```
[Unified Runtime] --(MQTT publish JSON)--> [MQTT Broker (fx Mosquitto)] --(subscribe)--> [Telegraf Agent] --(write)--> [Microsoft SQL Server]
```
Denne arkitektur repræsenterer en event-drevet, løst koblet integration, som muliggør høj skalerbarhed, lav latenstid og god fejltolerance.

## ⚙️ Trinvis konfiguration

### 🔧 1. Konfiguration af Unified Runtime – MQTT Publisher via JavaScript
```javascript
let payload = `{"timestamp":"${Unified.Time.Now()}","tag":"TempSensor1","value":${Tag_TempSensor1}}`;
MqttClient.Publish("unified/data", payload);
```
> Sørg for, at `MqttClient` er korrekt initialiseret med IP-adresse og port til MQTT-broker (som regel 1883). På nogle systemer skal forbindelsen muligvis justeres for at understøtte MQTT over TCP.

### 🧱 2. Opsætning af MQTT Broker – eksempel med Mosquitto
Installér MQTT-broker (Linux/Docker):
```bash
sudo apt install mosquitto mosquitto-clients
```
Redigér konfiguration for testformål:
```conf
default_listener 1883
allow_anonymous true
```
Start tjenesten og aktivér den permanent:
```bash
sudo systemctl enable mosquitto
sudo systemctl start mosquitto
```
Verificér, at broker modtager beskeder:
```bash
mosquitto_sub -t unified/data -v
```

### ⚙️ 3. Telegraf – MQTT input og SQL Server output
Installer Telegraf-agent på samme system som broker eller SQL Server. Tilføj følgende i `telegraf.conf`:
```toml
[[inputs.mqtt_consumer]]
  servers = ["tcp://localhost:1883"]
  topics = ["unified/data"]
  data_format = "json"
  tag_keys = ["tag"]
  json_string_fields = ["timestamp"]

[[outputs.sqlserver]]
  server = "Server=localhost;Port=1433;Database=WorkshopDB;User Id=unified;Password=demo123;"
  table = "TagLog"
  include_field = ["value"]
  fieldpass = ["value"]
  tagpass = ["tag"]
```
> Husk at SQL-tabellen `TagLog` skal matche strukturen fra MQTT-payloaden.

## 📂 Praktisk aktivitet
1. Opsæt Unified Runtime til at sende MQTT-data hvert minut med gyldigt JSON-format
2. Installer og konfigurer Mosquitto MQTT-broker lokalt eller på netværk
3. Bekræft korrekt MQTT-publishing med `mosquitto_sub`
4. Konfigurer Telegraf til at lytte på `unified/data` og skrive til SQL Server
5. Verificér import i databasen:
```sql
SELECT TOP 10 * FROM TagLog ORDER BY Timestamp DESC;
```
6. Gem dokumentation: konfigurationsfiler, skærmbilleder og output til portfolio

## 📌 Output til portfolio
- Eksempel på JSON-payload sendt fra Unified
- Konfigurationsuddrag fra `telegraf.conf` (både input og output)
- Skærmbillede fra SQL Server med indlæste rækker
- Diagram over MQTT–Telegraf–SQL-arkitektur og jeres implementering

## 🔍 Refleksionsspørgsmål
- Hvad er fordelene ved MQTT i forhold til ODBC, CSV og OPC UA, især ved mange distribuerede enheder?
- Hvad sker der, hvis MQTT-broker eller Telegraf er offline i en periode? Hvilke data tabes, og hvordan kan det undgås?
- Hvordan kan løsningen gøres mere robust med TLS, autentificering, adgangskontrol og QoS-niveauer?
- Hvordan kunne denne løsning skaleres til en cloud-platform som Azure IoT Hub eller AWS IoT Core?

---

I næste og afsluttende modul laver vi en tværgående sammenligning af de fire dataintegrationsmetoder, vi har arbejdet med: **ODBC**, **OPC UA**, **CSV/BULK INSERT** og **MQTT/Telegraf**. Fokus bliver på at matche teknisk løsning med driftssikkerhed, fleksibilitet og systemkompleksitet i virkelige scenarier.

