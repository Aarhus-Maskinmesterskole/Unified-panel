# 🧩 Modul 08 – Sammenligning af dataintegrationsmetoder

## 🎯 Læringsmål
Efter dette modul skal I kunne:
- Sammenligne fire centrale dataintegrationsmetoder i industrielle systemer
- Vurdere metoder ud fra parametre som robusthed, kompleksitet, realtid, fleksibilitet og skalerbarhed
- Udvælge og begrunde den bedst egnede metode til et givent scenarie
- Argumentere for brugen af hybrid- eller fallback-løsninger

## 🧪 De fire metoder vi har arbejdet med
| Metode         | Teknologi                       |
|----------------|---------------------------------|
| 1. ODBC        | Comfort/PC Runtime → MSSQL      |
| 2. OPC UA      | Unified → Node-RED → MSSQL      |
| 3. CSV-import  | Unified → CSV → BULK INSERT     |
| 4. MQTT        | Unified → MQTT → Telegraf → MSSQL |

## 🧠 Sammenligningskriterier
Hver metode vurderes efter følgende:
- **Realtidsegenskaber** (latens, polling, event-baseret)
- **Robusthed** (fejltolerance, genstart, netværksudfald)
- **Skalerbarhed** (antal enheder, datapunkter)
- **Fleksibilitet** (platforme, integrationer, cloud)
- **Sikkerhed** (TLS, brugerstyring, segmentering)
- **Kompleksitet i opsætning og drift**

## 📊 Sammenligningstabel (eksempel)
| Kriterium      | ODBC     | OPC UA     | CSV         | MQTT         |
|----------------|----------|------------|-------------|--------------|
| Realtid        | ★★☆☆☆     | ★★★☆☆       | ★☆☆☆☆         | ★★★★☆         |
| Robusthed      | ★★☆☆☆     | ★★★☆☆       | ★★☆☆☆         | ★★★★☆         |
| Skalerbarhed   | ★★☆☆☆     | ★★★☆☆       | ★★☆☆☆         | ★★★★★         |
| Fleksibilitet  | ★★☆☆☆     | ★★★☆☆       | ★★★☆☆         | ★★★★★         |
| Sikkerhed      | ★★☆☆☆     | ★★★★☆       | ★★☆☆☆         | ★★★★★         |
| Kompleksitet   | ★☆☆☆☆     | ★★★☆☆       | ★★☆☆☆         | ★★★★☆         |

## 🔍 Refleksionsspørgsmål
- Hvornår er det acceptabelt at bruge CSV og filbaseret integration?
- Hvornår er realtid vigtigere end kompleksitet i implementeringen?
- Hvordan kan man kombinere metoder – fx MQTT til cloud og ODBC lokalt?
- Hvad betyder valget af metode for langsigtet vedligeholdelse?

## 💬 Diskussion og output
- Lav en anbefaling til et konkret scenarie (f.eks. 10 enheder, delvist offline, lav latency ønskes)
- Sammenlign mindst to metoder og argumentér med kriterier
- Skitser jeres foretrukne IIoT-arkitektur og redegør for valg

## ✅ Opsummering
I har nu gennemgået både klassiske og moderne integrationsmetoder. Valg af metode afhænger af krav, ressourcer, vedligeholdelse og sikkerhed. Et velovervejet valg giver både højere driftsstabilitet og lettere fejlfinding – og er ofte afgørende for en skalérbar og fremtidssikret løsning.

