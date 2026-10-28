# Test-logigramme-Aircheck
Logigramme Aircheck

```mermaid
graph TD
    classDef sante fill:#f4ecf7,stroke:#8e44ad,stroke-width:2px,color:#000;
    classDef confort fill:#ebf5fb,stroke:#2980b9,stroke-width:2px,color:#000;
    classDef actVert fill:#abebc6,stroke:#239b56,stroke-width:2px,color:#000;
    classDef actRouge fill:#f5b7b1,stroke:#c0392b,stroke-width:2px,color:#000;
    classDef actGris fill:#f2f3f4,stroke:#7f8c8d,stroke-width:2px,color:#000;

    Start([Début du cycle de mesure]) --> Q1

    Q1{"[SPS30 & SGP41]<br>Pollution INTÉRIEURE<br>> Seuils d'alerte ?"}:::sante
    Q1 -- "OUI (Air intérieur vicié)" --> Q2
    Q1 -- "NON (Air intérieur sain)" --> Q3

    Q2{"[SPS30 & SGP41]<br>Pollution EXTÉRIEURE<br>< Pollution intérieure ?"}:::sante
    Q2 -- "OUI (L'extérieur est meilleur)" --> A1["🚨 NOTIFICATION : Il FAUT aérer !<br>Action : Ouvrir les fenêtres.<br>🔴 LED : ROUGE"]:::actVert
    Q2 -- "NON (L'extérieur est pire)" --> A2["❌ NOTIFICATION : NE PAS aérer !<br>Action : Activer purificateur d'air.<br> 🟠 LED : ORANGE"]:::actRouge

    Q3{"[DHT22]<br>Inconfort INTÉRIEUR ?<br>(T° > 26°C ou HR > 60% / < 40%)"}:::confort
    Q3 -- "OUI (Inconfort détecté)" --> Q4
    Q3 -- "NON (Confort optimal)" --> Q5

    Q4{"[DHT22]<br>L'air EXTÉRIEUR<br>améliore-t-il le confort ?"}:::confort
    Q4 -- "OUI" --> Q4b{"[SPS30 & SGP41]<br>Pollution EXTÉRIEURE<br>> Seuils d'alerte ?"}:::sante
    Q4b -- "NON (Air extérieur propre)" --> A3["✅ NOTIFICATION : Vous POUVEZ aérer.<br>Action : Réguler l'humidité/T°.<br>🟢 LED : VERT"]:::actVert
    Q4b -- "OUI (Air extérieur pollué)" --> A4["⚠️ NOTIFICATION : DÉCONSEILLÉ d'aérer.<br>Santé > Confort.<br>🔵 LED : BLEU"]:::actRouge
    
    Q4 -- "NON (Il fait pire dehors)" --> A5["🔒 NOTIFICATION : Maintenir fermé.<br>Action : VMC / Clim / Chauffage.<br>🔵 LED : BLEU"]:::actGris

    Q5{"[SPS30 & SGP41]<br>Pollution EXTÉRIEURE<br>> Seuils d'alerte ?"}:::sante
    Q5 -- "OUI (Pic de pollution dehors)" --> A6["⚠️ NOTIFICATION : DÉCONSEILLÉ d'aérer.<br>Action : Restez calfeutré.<br>🔵 LED : BLEU"]:::actRouge
    Q5 -- "NON (Tout est propre)" --> A7["✨ NOTIFICATION : Tout est OK !<br>Action : Aucune.<br>🟢 LED : VERT"]:::actGris
```
