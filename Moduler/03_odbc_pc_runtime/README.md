# 🧩 Modul 03 – ODBC fra Unified PC Runtime

## 🎯 Læringsmål
Efter dette modul skal I kunne:
- Etablere en ODBC-forbindelse fra Unified PC Runtime til Microsoft SQL Server (MSSQL)
- Benytte både DSN og DSN-less forbindelser i JavaScript-scripts
- Bruge JavaScript og evt. C# til at udføre grundlæggende og avancerede SQL-operationer
- Forstå forskellene og fordele mellem panel runtime og PC runtime
- Implementere robust fejlhåndtering, herunder logning og alarmering i Unified PC Runtime

## 🧠 Forudsætninger
Før I går i gang med øvelserne i dette modul, skal følgende være klarlagt og installeret:
- Unified PC Runtime skal være installeret og korrekt licenseret (TIA Portal V18 eller nyere)
- En Microsoft SQL Server skal være installeret, konfigureret og tilgængelig på netværket
- Der skal være installeret en ODBC-driver til SQL Server, fx Microsoft ODBC Driver 17 eller 18
- I skal have adgang til Windows-brugeren på PC'en for at oprette DSN'er eller arbejde med scripts og mapper
- En database kaldet `WorkshopDB` og en tabel `TagLog` skal være oprettet til testformål

## 🔎 Hvorfor vælge PC Runtime?
Der er væsentlige fordele ved at arbejde med Unified PC Runtime fremfor et Comfort Panel:
- **Øget ydeevne:** PC’er har væsentligt mere processorkraft og RAM, hvilket gør dem egnet til mere hyppige logninger og større mængder data
- **Fleksibilitet i programmering:** PC Runtime giver mulighed for at kombinere JavaScript med C# og .NET-funktionalitet
- **Avanceret fejlhåndtering:** Det er lettere at fejlsøge og logge fejl via filsystem og Windows-værktøjer
- **Bedre netværksintegration:** PC’en kan nemmere kobles op mod eksisterende IT-infrastruktur, backup-løsninger og sikkerhedssoftware

## ⚙️ Opret ODBC-forbindelse
Der er to måder at etablere en ODBC-forbindelse på: 

### 1. Brug af DSN (Data Source Name)
1. Åbn **ODBC Data Sources (64-bit)** via Kontrolpanelet eller Startmenuen
2. Under **System DSN**, klik "Tilføj"
3. Vælg fx **ODBC Driver 18 for SQL Server**
4. Navngiv forbindelsen `PC_SQL`, og angiv:
   - Server: `192.168.1.5`
   - Database: `WorkshopDB`
   - Bruger: `unified`, adgangskode: `demo123`

I Unified-script:
```javascript
const conn = Unified.Database.CreateConnection("DSN=PC_SQL");
```

### 2. DSN-less connection string (direkte i script)
```javascript
const conn = Unified.Database.CreateConnection(
  "Driver={ODBC Driver 18 for SQL Server};Server=192.168.1.5;Database=WorkshopDB;Uid=unified;Pwd=demo123;Encrypt=no;"
);
```
DSN-less er ofte lettere at deployere i systemer med mange maskiner, fordi den ikke kræver manuel DSN-konfiguration på hver PC.

## 📜 SQL-operationer i Unified PC Runtime

### Eksempel: Skriv en tagværdi til MSSQL
```javascript
conn.Execute("INSERT INTO TagLog (TagName, TagValue) VALUES ('FlowSensor', " + Tag_Flow + ")");
```
Dette script opretter en ny række i tabellen hver gang det køres. Husk at formatere dine værdier korrekt og undgå SQL injection i rigtige applikationer.

### Eksempel: Læs seneste værdi fra MSSQL og vis i HMI
```javascript
let result = conn.FetchAll("SELECT TOP 1 TagValue FROM TagLog ORDER BY Timestamp DESC");
Tag_LastFlow = result[0]["TagValue"];
```
Data kan nu vises i runtime via et visuelt objekt.

## 🛡️ Fejlhåndtering og logning i PC Runtime
Fejlhåndtering er afgørende i systemer, hvor data ikke må mistes.

```javascript
try {
  conn.Execute(...);
} catch (e) {
  Unified.Alarm("SQL_Fejl", e.message);
  fs.appendText("/log/sql_fejl.txt", e.message);
}
```

> 💡 Bonus: PC Runtime understøtter adgang til filsystemet, så I kan oprette en lokal logfil til fejlkoder eller hændelser. Det kan give stor værdi under idriftsættelse eller service.

## 📂 Aktivitet
1. Opret en `WorkshopDB`-database og `TagLog`-tabel i MSSQL:
```sql
CREATE TABLE TagLog (
  Timestamp DATETIME DEFAULT GETDATE(),
  TagName NVARCHAR(50),
  TagValue FLOAT
);
```
2. Konfigurer en DSN og/eller brug en DSN-less string i Unified Runtime
3. Skriv og læs fra databasen vha. scripts
4. Test fejlsituationer ved fx at ændre forbindelsesoplysninger eller slukke for netværk
5. Dokumentér output og reaktioner

## 📌 Output og dokumentation
- Unified PC Runtime-løsning, der skriver og læser korrekt til MSSQL
- Fejlhåndtering implementeret og logfil oprettet
- Screenshots og kodeeksempler i jeres portfolio
- Overvejelser omkring performance og robusthed

## 🔍 Refleksionsspørgsmål
- Hvordan oplevede I forskellen i ydeevne sammenlignet med et Comfort Panel?
- Er DSN eller DSN-less bedst i jeres kontekst, og hvorfor?
- Hvordan kan løsningen forbedres i et produktionsmiljø (buffer, retries, watchdogs)?

---

I næste modul skifter vi fra scripts til en grafisk og konfigurerbar tilgang uden programmering, hvor vi anvender **Siemens Industrial DataBridge** til at oprette en datakanal mellem Unified Runtime og SQL Server.

