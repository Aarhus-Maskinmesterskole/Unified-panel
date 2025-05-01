# 🧩 Modul 02 – ODBC fra Unified Comfort Panel (med og uden DSN)

## 🎯 Læringsmål
Efter dette modul skal I kunne:
- Forstå forskellen på DSN og DSN-less ODBC-forbindelser
- Bruge begge typer til at skrive/læse fra MSSQL via Unified JavaScript
- Implementere fejlhåndtering i Unified Runtime

## 🧠 Forudsætninger
- Comfort Panel med firmware ≥ V18 Update 3
- SQL Server installeret og aktiv
- ODBC Driver 17 eller 18 for SQL Server installeret

## ⚙️ ODBC-metoder
Unified Runtime understøtter to måder at etablere forbindelse til SQL Server:

### ✅ DSN-less (direkte i script)
```javascript
const conn = Unified.Database.CreateConnection(
  "Driver={ODBC Driver 18 for SQL Server};Server=192.168.1.5;Database=WorkshopDB;Uid=unified;Pwd=demo123;Encrypt=no;"
);
```

### ✅ Med DSN (foruddefineret via ODBC)
1. Gå til **Kontrolpanel > Administrative værktøjer > ODBC Data Sources (32/64-bit)**
2. Under **System-DSN** > Tilføj ny:
   - Vælg fx **ODBC Driver 18 for SQL Server**
   - Navngiv DSN: `WorkshopSQL`
   - Server: `192.168.1.5`
   - Database: `WorkshopDB`
   - Bruger: `unified`, adgangskode: `demo123`

```javascript
const conn = Unified.Database.CreateConnection("DSN=WorkshopSQL");
```

## 🛠️ Eksempel: Skriv og læs SQL

### 1. Skriv data til MSSQL
```javascript
conn.Execute(
  "INSERT INTO TagLog (TagName, TagValue) VALUES ('TempSensor1', " + Tag_TempSensor1 + ")"
);
```

### 2. Læs seneste værdi fra MSSQL
```javascript
let result = conn.FetchAll("SELECT TOP 1 TagValue FROM TagLog ORDER BY Timestamp DESC");
Tag_LastValue = result[0]["TagValue"];
```

### 3. Fejlhåndtering
```javascript
try {
  conn.Execute("...");
} catch (e) {
  Unified.Alarm("DBError", e.message);
}
```

## 📂 Aktivitet
1. Opret database og tabel i SQL Server:
```sql
CREATE TABLE TagLog (
  Timestamp DATETIME DEFAULT GETDATE(),
  TagName NVARCHAR(50),
  TagValue FLOAT
);
```
2. Implementér både DSN og DSN-less forbindelser i HMI-script
3. Test INSERT og SELECT via Runtime
4. Afprøv fejlhåndtering (f.eks. forkert login, netværkskabel udtaget)

## 📌 Output
- Unified Runtime der skriver og læser korrekt fra SQL Server
- Kode i jeres portfolio med begge forbindelsesmetoder
- Screenshot af DSN-konfiguration

## 🔍 Refleksionsspørgsmål
- Hvornår giver DSN fordele fremfor DSN-less?
- Hvad er konsekvenserne af netværksudfald ved denne løsning?
- Hvordan kan I opsætte fallback eller overvågning?

---

I næste modul udvider vi med Unified PC Runtime og ser, hvordan PC'en kan give mere fleksibilitet og kapacitet i datalogging.

