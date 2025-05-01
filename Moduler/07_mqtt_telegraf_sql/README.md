# 🧩 Modul 07 – MQTT → Telegraf → MSSQL

## 🎯 Læringsmål
Efter dette modul skal I kunne:
- Forstå, hvordan MQTT fungerer som en publish/subscribe-baseret kommunikationsprotokol
- Forklare forskellen mellem MQTT og tidligere pull-baserede løsninger som CSV og ODBC
- Konfigurere Unified Runtime til at sende måledata til en MQTT-broker
- Installere og konfigurere Telegraf til at abonnere på MQTT-data og videresende dem til Microsoft SQL Server
- Evaluere fordele og ulemper ved at anvende MQTT og Telegraf i industrielt dataintegrationsarbejde

## 🧠 Forudsætninger
Før I går i gang med dette modul, skal følgende være opfyldt:
- I har adgang til en MQTT-broker (f.eks. Mosquitto installeret lokalt eller på netværket)
- Unified Runtime kører på et Comfort Panel eller en PC, og er i stand til at sende MQTT-data (via JavaScript eller Siemens Edge Connect)
- Telegraf-agent er installeret og har skriveadgang til SQL Server
- I er fortrolige med JSON-struktur, portnumre og grundlæggende netværksforbindelser
- SQL Server indeholder en `TagLog`-tabel med passende felter (fx `timestamp`, `tag`, `value`)

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

