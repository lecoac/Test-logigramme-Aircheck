# Test-logigramme-Aircheck
Logigramme Aircheck

```mermaid
graph TD
    classDef sante fill:#f4ecf7,stroke:#8e44ad,stroke-width:2px,color:#000;
    classDef confort fill:#ebf5fb,stroke:#2980b9,stroke-width:2px,color:#000;
    classDef equip fill:#fcf3cf,stroke:#f1c40f,stroke-width:2px,color:#000; %% Jaune pour les équipements
    classDef actVert fill:#abebc6,stroke:#239b56,stroke-width:2px,color:#000;
    classDef actRouge fill:#f5b7b1,stroke:#c0392b,stroke-width:2px,color:#000;
    classDef actGris fill:#f2f3f4,stroke:#7f8c8d,stroke-width:2px,color:#000;

    Start([Début du cycle de mesure]) --> Q1

    Q1{"[SPS30 & SGP41]<br>Pollution INTÉRIEURE<br>> Seuils d'alerte ?"}:::sante
    Q1 -- "OUI (Air intérieur vicié)" --> Q2
    Q1 -- "NON (Air intérieur sain)" --> Q3

    Q2{"[SPS30 & SGP41]<br>Pollution EXTÉRIEURE<br>< Pollution intérieure ?"}:::sante
    Q2 -- "OUI (L'extérieur est meilleur)" --> A1["🚨 NOTIFICATION : Il FAUT aérer !<br>Action : Ouvrir les fenêtres.<br>🔴 LED : ROUGE"]:::actVert
    Q2 -- "NON (L'extérieur est pire)" --> Eq1
    
    Eq1{"[ÉQUIPEMENT]<br>Purificateur d'air<br>disponible ?"}:::equip
    Eq1 -- "OUI" --> A2a["❌ NOTIFICATION : NE PAS aérer !<br>Action : Activer le purificateur d'air.<br>🟠 LED : ORANGE"]:::actRouge
    Eq1 -- "NON" --> A2b["❌ NOTIFICATION : NE PAS aérer !<br>Action : Maintenir fermé (Isoler).<br>🟠 LED : ORANGE"]:::actRouge

    Q3{"[DHT22]<br>Inconfort INTÉRIEUR ?<br>(T° > 26°C ou HR > 60% / < 40%)"}:::confort
    Q3 -- "OUI (Inconfort détecté)" --> Q4
    Q3 -- "NON (Confort optimal)" --> Q5

    Q4{"[DHT22]<br>L'air EXTÉRIEUR<br>améliore-t-il le confort ?"}:::confort
    Q4 -- "OUI" --> Q4b{"[SPS30 & SGP41]<br>Pollution EXTÉRIEURE<br>> Seuils d'alerte ?"}:::sante
    Q4b -- "NON (Air extérieur propre)" --> A3["✅ NOTIFICATION : Vous POUVEZ aérer.<br>Action : Réguler l'humidité/T°.<br>🟢 LED : VERT"]:::actVert
    Q4b -- "OUI (Air extérieur pollué)" --> A4["⚠️ NOTIFICATION : DÉCONSEILLÉ d'aérer.<br>Santé > Confort.<br>🟠 LED : ORANGE"]:::actRouge
    
    Q4 -- "NON (Il fait pire dehors)" --> Eq2
    
    Eq2{"[ÉQUIPEMENT]<br>VMC ou Climatisation<br>disponible ?"}:::equip
    Eq2 -- "OUI" --> A5a["🔒 NOTIFICATION : Maintenir fermé.<br>Action : Activer VMC / Clim.<br>🔵 LED : BLEU"]:::actGris
    Eq2 -- "NON" --> A5b["🔒 NOTIFICATION : Maintenir fermé.<br>Action : Aucune (Attendre).<br>🔵 LED : BLEU"]:::actGris

    Q5{"[SPS30 & SGP41]<br>Pollution EXTÉRIEURE<br>> Seuils d'alerte ?"}:::sante
    Q5 -- "OUI (Pic de pollution dehors)" --> A6["⚠️ NOTIFICATION : DÉCONSEILLÉ d'aérer.<br>Action : Restez calfeutré.<br>🔵 LED : BLEU"]:::actRouge
    Q5 -- "NON (Tout est propre)" --> A7["✨ NOTIFICATION : Tout est OK !<br>Action : Aucune.<br>🟢 LED : VERT"]:::actGris
```
## Seuils d'Alerte et Références

Le système s'appuie sur des normes sanitaires mondiales et les spécifications des constructeurs pour déclencher les alertes de qualité de l'air et de confort.

| Paramètre | Capteur | Seuil d'alerte intérieur | Source & Référence |
| :--- | :--- | :--- | :--- |
| **PM2.5** | SPS30 | > 15 µg/m³ | [Directives OMS 2021](https://www.who.int/publications/i/item/9789240034228) - Résumé d'orientation, Tableau 1. |
| **PM10** | SPS30 | > 45 µg/m³ | [Directives OMS 2021](https://www.who.int/publications/i/item/9789240034228) - Résumé d'orientation, Tableau 1. |
| **Indice COV** | GY-SGP41 | > 150 (sur 500) | [Datasheet Sensirion SGP41](https://sensirion.com/media/documents/5FE867AE/61E96FE8/Sensirion_Gas_Sensors_SGP41_Datasheet.pdf) - Section "VOC/NOx Algorithm" (100 = Moyenne, >150 = Détérioration). |
| **Indice NOx** | GY-SGP41 | > 50 (sur 500) | [Datasheet Sensirion SGP41](https://sensirion.com/media/documents/5FE867AE/61E96FE8/Sensirion_Gas_Sensors_SGP41_Datasheet.pdf) - Section "VOC/NOx Algorithm" (1 = Fond normal). |
| **Humidité (HR)** | DHT22 | < 40% ou > 60% | [Guide ADEME "Un air sain chez soi"](https://www.grandnancy.eu/fileadmin/fichiers/VIVRE_ET_HABITER/Environnement/Qualite_air/guide-pratique-un-air-sain-chez-soi.pdf) - Page 16 "Évacuez l'humidité excessive". |
| **Température (T)**| DHT22 | < 19°C ou > 26°C | [Guide ADEME "Un air sain chez soi"](https://www.grandnancy.eu/fileadmin/fichiers/VIVRE_ET_HABITER/Environnement/Qualite_air/guide-pratique-un-air-sain-chez-soi.pdf) - Limites de confort été/hiver. |

---

## Méthodes de Calcul et Confort Thermique

Le capteur **DHT22** remonte deux valeurs brutes : la Température ($T$ en °C) et l'Humidité Relative ($HR$ en %).
Pour évaluer le confort humain réel (qui dépend de l'effet combiné de la chaleur et de l'humidité), le système calcule la **température de rosée** puis l'**Humidex** (température ressentie).

### Explication simple
Quand l'air est très humide, la sueur s'évapore moins bien. Le corps humain n'arrive plus à se refroidir naturellement. C'est pourquoi 25°C avec 80% d'humidité est ressenti comme beaucoup plus étouffant que 25°C avec 40% d'humidité. Les calculs ci-dessous permettent au logiciel de "comprendre" cet étouffement.

### 1. Calcul de la Pression de vapeur et du Point de Rosée
On utilise la formule météorologique de Magnus-Tetens pour déterminer le point de condensation. 

**Étape A : Calcul de la variable intermédiaire $\alpha$**

$\alpha = \frac{17,27 \times T}{237,7 + T} + \ln\left(\frac{HR}{100}\right)$

**Étape B : Calcul de la Température de rosée ($T_d$ en °C)**

$T_d = \frac{237,7 \times \alpha}{17,27 - \alpha}$

### 2. Calcul de l'Humidex (Température ressentie)
À partir de la température et de l'humidité, on détermine la pression de vapeur d'eau ($e$ en hPa), puis on calcule l'indice Humidex.

**Calcul de la pression de vapeur d'eau ($e$) :**

$e = 6,112 \times 10^{\left( \frac{7,5 \times T_d}{237,7 + T_d} \right)}$

**Formule finale de l'Humidex ($H$) :**

$H = T + 0,5555 \times (e - 10)$

**Interprétation du résultat Humidex par le logiciel :**
*   **H < 29 :** Aucun inconfort (Zone verte).
*   **30 < H < 39 :** Inconfort léger à modéré. Nécessite de vérifier si l'air extérieur est plus frais pour aérer.
*   **H > 40 :** Inconfort extrême (Danger).
