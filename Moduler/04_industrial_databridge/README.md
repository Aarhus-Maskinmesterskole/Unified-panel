# 🧩 Modul 04 – Siemens Industrial DataBridge (IDB) til MSSQL

## 🎯 Læringsmål
Efter dette modul skal I kunne:
- Installere og konfigurere Siemens Industrial DataBridge (IDB) fra bunden
- Etablere både DSN- og DSN-less ODBC-forbindelser til Microsoft SQL Server
- Oprette og teste et komplet dataflow mellem Unified Runtime (PLC/HMI) og SQL Server
- Reflektere over forskellen mellem scriptbaserede og konfigurerbare løsninger og vurdere robusthed og skalerbarhed

## 🧠 Forudsætninger
- Industrial DataBridge (IDB) installeret på en Windows-PC med administrativ adgang (kræver licens)
- SQL Server Express eller fuldversion installeret og netværksadgang fra IDB-PC
- Unified Runtime tilgængelig (Comfort Panel eller Unified PC Runtime)
- ODBC-driver til SQL Server installeret (Microsoft ODBC Driver 17/18 anbefales)
- Oprettet testdatabase med tabel `TagLog` i SQL Server:
```sql
CREATE TABLE TagLog (
  Timestamp DATETIME DEFAULT GETDATE(),
  TagName NVARCHAR(50),
  TagValue FLOAT
);
```

## ⚙️ Komplet konfiguration i IDB

### 1. Opret forbindelse til SQL Server

#### Mulighed A: DSN (Data Source Name)
**Sådan oprettes DSN:**
1. Gå til **ODBC Data Sources (64-bit)** i Windows
2. Under **System DSN**, klik “Tilføj”
3. Vælg **ODBC Driver 18 for SQL Server**
4. Indtast følgende:
   - DSN-navn: `IDB_SQL`
   - Server: `192.168.1.5`
   - Database: `WorkshopDB`
   - Autentificering: SQL Login (`unified` / `demo123`)
5. Tryk “Test Connection” for at verificere

**I IDB:**
- Vælg "ODBC" som datamål
- Brug forbindelsestypen: “SQL Server via ODBC (DSN)”
- Udpeg `IDB_SQL` fra dropdown-listen
- Test forbindelsen igen via IDB-grænsefladen

#### Mulighed B: DSN-less connection string
1. Åbn IDB og vælg "SQL Server via ODBC (DSN-less)"
2. Indsæt følgende i tekstfeltet:
```text
Driver={ODBC Driver 18 for SQL Server};Server=192.168.1.5;Database=WorkshopDB;Uid=unified;Pwd=demo123;Encrypt=no;
```
3. Tryk på "Test Connection" og kontroller at forbindelsen er succesfuld

> 📌 **Bemærk:** DSN-less forbindelser er lettere at versionere og udrulle, da de kan gemmes direkte i IDB-konfigurationsfilen.

### 2. Opret forbindelse til datakilde (Unified Runtime)

**Mulige forbindelsestyper:**
- S7-1500 via S7-kommunikation
- OPC UA Server (aktiveret i PLC eller Unified PC Runtime)

**Eksempel med OPC UA:**
1. Vælg “OPC UA Client” som kilde
2. Indtast adresse: `opc.tcp://192.168.1.10:4840`
3. Browse efter relevante tags (fx `Tag_TempSensor1`)
4. Vælg de tags du vil bruge som kilde

### 3. Opret datakanal (Mapping)
1. Klik “New Transfer Task” → vælg kilde og destination
2. Knyt fx:
   - `Tag_TempSensor1` → `TagLog.TagValue`
   - Opret en statisk kolonne `"Tag_TempSensor1"` til `TagName`
   - Brug `NOW()` til `Timestamp` eller SQL’s default
3. Vælg datatype-konvertering hvis nødvendigt (float → float)
4. Sæt trigger til:
   - OnChange (ændring i værdien)
   - Cyclic (fx hvert 5. sekund)

### 4. Gem og start datakanal
- Gem konfigurationen som `.idbproj`
- Start overførsel og overvåg forbindelsesstatus
- Bekræft at data vises i SQL Server:
```sql
SELECT * FROM TagLog ORDER BY Timestamp DESC;
```

## 📂 Aktivitet
- Opret både DSN og DSN-less forbindelse til SQL
- Konfigurer forbindelse til Unified Runtime via OPC UA eller S7
- Opret en komplet datakanal med logning af en sensorværdi
- Test forbindelsen ved at ændre værdien i PLC eller HMI
- Dokumentér jeres løsning med screenshots, forklaringer og evt. fejlsøgning

## 📌 Output
- `.idbproj` projektfil
- Screenshot af DSN og DSN-less setup
- Screenshot af aktiv datakanal og data i MSSQL
- Portfolio med kort beskrivelse af valgt metode og refleksion

## 🔍 Refleksionsspørgsmål
- Hvilken metode valgte I (DSN eller DSN-less) og hvorfor?
- Hvad er fordelene ved IDB kontra scripts i Unified?
- Hvordan kan I gøre løsningen mere robust mod netværksfejl eller database-nedetid?

---

I næste modul undersøger vi hvordan man kan bruge OPC UA sammen med Node-RED til at hente data fra Unified Runtime og gemme dem i MSSQL uden brug af Siemens-software.

