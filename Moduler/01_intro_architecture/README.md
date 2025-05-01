# 🧩 Modul 01 – Introduktion og Arkitektur

## 🎯 Læringsmål
Efter dette modul skal I kunne:
- Beskrive forskellen på Unified Comfort Panel og Unified PC Runtime
- Forstå hvordan Unified Runtime kan kommunikere med MSSQL
- Udpege fordele og ulemper ved forskellige arkitekturer
- Forklare hvor og hvordan data kan flyde fra PLC/HMI til database

## 🧠 Baggrund
Inden vi begynder at kode eller konfigurere, er det vigtigt at forstå **hvordan komponenterne hænger sammen**.

I denne workshop arbejder vi med:
- **Unified Comfort Panel** eller **Unified PC Runtime** som frontend og gateway
- **Microsoft SQL Server (lokal eller netværk)** som datalager
- Mulige mellemled: OPC UA, MQTT, CSV, ODBC, middleware

## 🏗️ Arkitekturer og datastrømme

### A. Direkte ODBC fra Unified Runtime
- Runtime opretter ODBC-forbindelse til SQL og skriver læsninger direkte
- Sker via script (JavaScript eller C#)

**Fordel:** Simpelt, direkte
**Ulempe:** Ingen buffer ved netværksfejl

### B. Konfigurerbar middleware
- Unified Runtime eller PLC eksponerer data (OPC UA, S7)
- En ekstern applikation (Node-RED, IDB, Kepware) henter og skriver til MSSQL

**Fordel:** Mere robust og skalerbart
**Ulempe:** Flere komponenter og krav til konfiguration

### C. Edge- og Cloud-baserede flows
- Unified sender data via MQTT til broker
- Telegraf-agent abonnerer og videreskriver til MSSQL

**Fordel:** Moderne IIOT-pattern, cloud-ready
**Ulempe:** Kræver MQTT-infrastruktur

## 🔍 Refleksionsspørgsmål
1. Hvilke arkitekturer kender I fra tidligere?
2. Hvad er forskellen på en "pull" og "push" model?
3. Hvilke krav stiller industrien til driftssikre dataflows?

## 📌 Aktivitet
- Gennemgå arkitekturskitsen (udleveres)
- Marker i grupper hvordan data flyder, og hvor fejlkilder kan opstå
- Diskutér hvordan man kan sikre datastrømmen (buffer, retry, watchdogs)

## 📂 Output
- Diagram med jeres bud på en robust Unified → MSSQL-arkitektur
- Kort notat med opsummering af valg og begrundelser

---

I næste modul går vi i gang med at bruge **JavaScript og ODBC** til at skrive direkte til MSSQL fra Unified Runtime. Sørg for at have SQL Server installeret og ODBC-driveren klar på PC'en, hvis I arbejder med Unified PC Runtime.


