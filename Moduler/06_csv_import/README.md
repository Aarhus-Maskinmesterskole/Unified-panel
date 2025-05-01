# ⚙️ Komponentopsætning: OPC UA og Node-RED til MSSQL (Udvidet)

## 1. Unified Runtime (OPC UA-server)

**Systemkrav og forberedelse:**
- Unified Comfort Panel eller Unified PC Runtime med firmware, der understøtter OPC UA-serverfunktionalitet
- TIA Portal (V17 eller nyere) installeret til konfiguration
- Adgang til projektfil, hvor relevante tags er oprettet og kan eksponeres

**Formål:**
OPC UA-serveren i Unified Runtime muliggør sikker og standardiseret datatilgængelighed fra HMI/PLC til tredjepartsapplikationer som fx Node-RED. Dette modul fokuserer på opsætningen, så tags kan læses live fra eksterne systemer.

**Trinvis konfiguration i TIA Portal:**
1. Navigér til fanen **Kommunikation** > **OPC UA Server** under projektets runtime-indstillinger
2. Marker "Aktivér OPC UA-server"
3. Indtast endpoint-adresse, f.eks.:
   ```
   opc.tcp://192.168.1.100:4840
   ```
4. Vælg ønsket sikkerhedsniveau:
   - `None` (anbefales til test og undervisning)
   - `Basic256Sha256` med certifikatudveksling (kræver nøglepar og godkendelse)
5. Klik på "Generér certifikat" og accepter det lokalt for test
6. Gå til **Datablokke**, og marker relevante variabler som:
   - "Accessible from HMI"
   - "Accessible from OPC UA"
   - Tildel navngivne NodeID’er hvis nødvendigt (fx `ns=2;s=Tag_TempSensor1`)
7. Kompiler og download til panel eller PC Runtime

> 💡 Tip: Hvis OPC UA-serveren ikke starter, kan det skyldes IP-konflikt, portblokering eller manglende certifikatudveksling.

## 2. Node-RED konfiguration

**Forudsætninger:**
- Node-RED installeret lokalt, i Docker-container eller på en edge-PC
- Tilgængelig forbindelse til Unified Runtime via netværk
- Følgende nodes installeret:
  - `node-red-contrib-opcua` (for at kunne tilgå OPC UA servere)
  - `node-red-node-mssql` (for integration med Microsoft SQL Server)

### A. OPC UA input (Node-RED som klient)

**Opsætning:**
1. Træk en `OPC UA Client` node ind på flowet
2. Dobbeltklik for at åbne konfigurationen
3. Indtast endpoint:
   ```
   opc.tcp://192.168.1.100:4840
   ```
4. Tryk “Browse” og vælg tag (eksempel):
   ```
   ns=2;s=Tag_TempSensor1
   ```
5. Vælg "Poll interval": 5000 ms (5 sekunder) eller brug "Subscribe on change" hvis din runtime understøtter det
6. Tilføj en `function` node direkte efter OPC UA-node med følgende kode:
```javascript
try {
    msg.payload = msg.payload.value;
    return msg;
} catch (err) {
    node.error("Fejl ved læsning af OPC UA-værdi", msg);
    return null;
}
```
Dette udtrækker selve værdien fra OPC UA-pakken og konverterer det til et simpelt tal, som kan sendes videre til SQL. Den inkluderede `try/catch`-blok beskytter mod runtime-fejl og hjælper ved fejlsøgning.

### B. SQL output

**Opsætning af MSSQL-node:**
1. Træk en `MSSQL` node ind i flowet
2. Konfigurer databasen:
   - **Server**: `192.168.1.5`
   - **Port**: `1433`
   - **Database**: `WorkshopDB`
   - **Brugernavn**: `unified`
   - **Adgangskode**: `demo123`

💡 Sørg for, at Microsoft ODBC Driver for SQL Server (f.eks. version 17 eller 18) er installeret på det system, hvor Node-RED kører. MSSQL-noden bruger denne driver til at kommunikere med databasen.

3. SQL-kommando (uden timestamp):
```sql
INSERT INTO TagLog (TagName, TagValue) VALUES ('TempSensor1', {{payload}})
```

4. SQL-kommando (med automatisk timestamp via SQL Server):
```sql
INSERT INTO TagLog (TagName, TagValue, Timestamp) VALUES ('TempSensor1', {{payload}}, GETDATE())
```
> Bemærk: `{{payload}}` bliver automatisk erstattet af det indkommende tal fra `function`-noden.

### C. Fuldt flow eksempel

Et simpelt Node-RED flow kunne se således ud:
```text
[OPC UA Client node]
      ↓
[Function node: udtræk værdi]
      ↓
[MSSQL node]
```
For fejlhåndtering og overvågning kan du tilføje:
- `debug` node (for at se output af hvert trin)
- `catch` node (fanger fejl i flowet)
- `status` node (viser forbindelsesstatus og hjælper med fejlsøgning)

### D. Eksempel på komplet flowkode (eksporteret JSON)
> (Dette afsnit kan evt. laves som `.json` fil til import i Node-RED – valgfrit i din undervisning)

---

## ✅ Konklusion
Denne komponentopsætning muliggør en moderne og fleksibel integration mellem Siemens Unified Runtime og MSSQL ved brug af OPC UA og Node-RED. Arkitekturen er oplagt i edge- og mellemstore IIoT-scenarier, hvor man ønsker let deployment, standardprotokoller og scriptfri SQL-kommunikation.

Fordelen ved denne metode er:
- Nem adgang til PLC/HMI-data
- Ingen Siemens-specifik software efter HMI
- Fleksibel visualisering og fejlhåndtering i Node-RED

> ⚠️ Overvej altid at sikre forbindelser med certifikat og bruge `Encrypt=yes` i SQL-forbindelser i produktionsmiljøer.

