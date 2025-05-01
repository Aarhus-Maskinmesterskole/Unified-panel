# 🧩 Modul 02 – ODBC-forbindelse fra Unified Comfort Panel

## 🎯 Læringsmål
Efter dette modul skal I kunne:
- Forstå hvordan man skriver SQL fra Unified JavaScript
- Oprette og bruge ODBC-forbindelse fra et Comfort Panel
- Skrive og læse data til og fra MSSQL fra Runtime
- Håndtere fejl og vise fejlmeddelelser i HMI

## 🧠 Forudsætninger
- Comfort Panel med firmware ≥ V18 Update 3
- Microsoft SQL Server (f.eks. SQL Express) installeret og aktiv
- ODBC Driver 17 eller 18 for SQL Server
- En ODBC connection-string til MSSQL-serveren (kan være DSN eller DSN-less)

## ⚙️ Tekniske komponenter
- Unified Runtime JavaScript API: `Unified.Database`
  - `CreateConnection()`
  - `Execute()`
  - `FetchAll()`
- SQL Server database og tabel:
  ```sql
  CREATE TABLE TagLog (
    Timestamp DATETIME DEFAULT GETDATE(),
    TagName NVARCHAR(50),
    TagValue FLOAT
  );
  ```

## 🛠️ Trin-for-trin

### 1. Opret ODBC-forbindelse
**Eksempel:** DSN-less string i script:
```javascript
const conn = Unified.Database.CreateConnection(
  "Driver={ODBC Driver 18 for SQL Server};Server=192.168.1.5;Database=WorkshopDB;Uid=unified;Pwd=demo123;Encrypt=no;"
);
```

### 2. Skriv data til SQL
```javascript
conn.Execute(
  "INSERT INTO TagLog (TagName, TagValue) VALUES ('TempSensor1', " + Tag_TempSensor1 + ")"
);
```

### 3. Læs data og vis det i HMI
```javascript
let result = conn.FetchAll("SELECT TOP 1 TagValue FROM TagLog ORDER BY Timestamp DESC");
Tag_LastValue = result[0]["TagValue"];
```

### 4. Fejlhåndtering
```javascript
try {
  conn.Execute("...");
} catch (e) {
  Unified.Alarm("DBError", e.message);
}
```

## 📂 Aktivitet
1. Opret database og tabel i SQL Server
2. Tilføj et JavaScript i HMI med INSERT og SELECT
3. Test forbindelsen og valider at data skrives korrekt
4. Indsæt fejlmeddelelser som alarm eller diagnose-log i HMI

## 📌 Output
- Kørende panel, der logger temperatur (eller andet tag) til MSSQL hvert minut
- Et skærmbillede med seneste værdi læst fra databasen
- Kodeeksempel dokumenteret i jeres portfolio

## 🔍 Refleksionsspørgsmål
- Hvordan sikrer I, at forbindelsen ikke fejler under drift?
- Hvordan kan I forbedre fejlhåndteringen?
- Hvad er fordelene og begrænsningerne ved denne løsning?

---

I næste modul arbejder vi videre med samme metode, men flytter runtime til en PC og udvider med ekstra værktøjer og ydeevne.

