# 🧱 Moduler – Unified → MSSQL Workshop

Denne mappe indeholder de tekniske moduler, som udgør rygraden i workshoppen om kommunikation mellem **Siemens Unified Comfort Panel / PC Runtime** og **Microsoft SQL Server (MSSQL)**.

Hvert modul fokuserer på en specifik metode til dataoverførsel, og giver jer hands-on erfaring med opsætning, dataskrivning, fejlhåndtering og vurdering af tekniske løsninger.

## 🧩 Oversigt over moduler

| Modulmappe | Indhold |
|------------|---------|
| `01_intro_architecture` | Unified Runtime-typer, SQL-arkitektur, overview over muligheder |
| `02_odbc_panel` | JavaScript + ODBC direkte fra Unified Comfort Panel |
| `03_odbc_pc_runtime` | ODBC-forbindelse via Unified PC Runtime og C#/JavaScript |
| `04_industrial_databridge` | Konfigurerbar løsning med Siemens Industrial DataBridge |
| `05_opcua_nodered` | OPC UA-kommunikation til Node-RED og videreskrivning til MSSQL |
| `06_csv_import` | Eksport af CSV fra Unified + import i SQL med BULK INSERT eller PowerShell |
| `07_mqtt_telegraf_sql` | MQTT publicering og dataflow via Telegraf til MSSQL |
|  `08_Sammenligning_af_dataintegrationsmetoder` | Sammenligning af dataintegrationsmetoder |

## 📁 Struktur i hvert modul

Hver undermappe indeholder:

- `README.md` – Introduktion, læringsmål og opsætning
- `opgave.md` – Praktisk øvelse og evalueringskriterier
- `kode/` – Scriptfiler, connection-strings og konfigurationsfiler
- `billeder/` – Screenshots af opsætning og resultater (valgfrit)
- `refleksion.md` – Guide til afsluttende vurdering og overvejelser

## 🧠 Formål

Modulerne er designet til at:

- Udvikle jeres forståelse af forskellige kommunikationsmetoder
- Træne jer i praktisk fejlfinding og dokumentation
- Gøre jer i stand til at vælge den rigtige løsning til konkrete industrielle behov

## 📝 Portfolio og aflevering

Undervejs skal I dokumentere jeres arbejde med:

- Opsætning og testresultater
- Fejlhåndtering og løsninger
- Vurdering af hver løsnings fordele og begrænsninger

Dokumentationen skal bruges i jeres **gruppeportfolio**, som danner grundlag for bedømmelse.

---

God arbejdslyst! Husk at stille spørgsmål, samarbejde og dele jeres indsigter med hinanden.
