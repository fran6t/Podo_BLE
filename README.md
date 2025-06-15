# 👣 Enregistreur de pas autonome BLE (Seeed XIAO BLE Sense)

[![Licence MIT](https://img.shields.io/badge/Licence-MIT-green.svg)](#licence)

## 🎯 Objectif

Créer un dispositif **compact, autonome et discret** à insérer dans une semelle de chaussure pour **surveiller l’activité de marche** d’un utilisateur :

- Activité de jour (~4 h de marche)
- Détection de mouvements nocturnes (ex. levers pour aller aux toilettes)
- Transfert des données par BLE via smartphone
- Autonomie d’au moins 14 jours
- Enregistrement minimaliste et basse consommation

---

## 🧠 Fonctionnement général

- 🧩 Basé sur la carte **Seeed Studio XIAO BLE Sense** (nRF52840 + IMU LSM6DS3TR-C)
- 🧠 Utilise le **compteur de pas matériel intégré** du capteur
- ⏱️ Le microcontrôleur se réveille **toutes les 5 minutes en journée** et **toutes les 15 minutes la nuit**
- 💾 Il enregistre la valeur du compteur **uniquement si elle a changé**
- 📲 Interaction utilisateur via **Bluetooth activé uniquement au démarrage**
- 🧠 Deux cas d’usage :
  - `START timestamp` → remise à zéro du compteur + lancement du suivi
  - `DUMP` → transmission des données enregistrées

---

## ⚙️ Architecture matérielle

| Composant                     | Description                                  |
|------------------------------|----------------------------------------------|
| Seeed Studio XIAO BLE Sense  | Microcontrôleur nRF52840 + IMU LSM6DS3       |
| Batterie LiPo 3.7 V 120 mAh  | Autonomie ≥ 30 jours                         |
| Connexion BLE (nRF Connect)  | Configuration & extraction                   |
| Micro-USB-C                  | Recharge via chargeur BQ25100 intégré        |

---

## 📦 Flux de données

### 1. Démarrage (via **reset**)
- Active la publicité BLE pour 60 s
- Attend une commande depuis nRF Connect :

#### Cas 1 – `START yyyy-mm-ddThh:mm:ssZ`
- Réinitialise la mémoire et le compteur de pas
- Enregistre le timestamp de référence
- Coupe le BLE et démarre le suivi

#### Cas 2 – `DUMP`
- Envoie les données via BLE Notify
- Coupe le BLE et continue le comptage

---

## 🧾 Stockage

| Format                  | Détail                                  |
|-------------------------|-----------------------------------------|
| Un enregistrement       | 2 octets timestamp delta + 2 octets pas |
| Fréquence (jour)        | Toutes les 5 min si variation           |
| Fréquence (nuit)        | Toutes les 15 min si variation          |
| Enregistrement typique  | ~150–200 points / jour                  |
| Taille estimée 14 jours | ~1.5 Ko à ~3 Ko                         |
| Mémoire dispo (flash)   | ≥ 512 Ko                                |

---

## 🔋 Gestion de l'alimentation

### 🔢 Consommation estimée

| Source                    | Valeur typique |
|---------------------------|----------------|
| IMU actif (24h)           | ~2.88 mAh      |
| MCU en veille             | ~0.05 mAh      |
| Réveils (224/jour)        | ~0.003 mAh     |
| BLE au boot uniquement    | ~0.08 mAh (ponctuel) |
| **Total / jour**          | **~2.93 mAh** ✅

### 🪫 Batterie recommandée : **LiPo 3.7 V – 120 mAh**

> Exemple : [LiPo 120 mAh 401822 – Amazon](https://www.amazon.fr/401822-Liter-l%C3%A9lectronique-Rechargeable-t%C3%A9l%C3%A9phone/dp/B0892MS2D1)

| Capacité utile (90%) | Autonomie estimée |
|----------------------|--------------------|
| ~108 mAh             | **~36 jours** ✅   |

---

## 📱 Interaction BLE

| Caractéristique     | Type    | UUID (exemple)           | Description                      |
|---------------------|---------|--------------------------|----------------------------------|
| Commande            | Write   | `1234...`                | `START`, `DUMP`                  |
| Données de pas      | Notify  | `5678...`                | Blocs horodatés (20 octets max) |
| Niveau batterie     | Read    | `0x2A19` (standard)      | Tension estimée via ADC interne |

Utilisez **nRF Connect (iOS/Android)** pour piloter la carte.

---

## 📐 Positionnement et fixation

- Carte à fixer **solidement** dans la semelle (voûte plantaire, talon)
- Orientation **non critique**
- Le compteur matériel fonctionne dès qu’un **mouvement rythmique** est détecté

---

## 🛠️ Outils

- 🔧 **IDE** : Arduino IDE + Seeed nRF52 Boards
- 📲 **App mobile** : [nRF Connect](https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-mobile)
- 📊 **Export JSON** : depuis nRF Connect 
- 🖼️ **Modélisation boîtier** : FreeCAD

---

## 🗺️ Roadmap

- [x] Compteur de pas matériel actif 24h/24
- [x] Enregistrement conditionnel toutes les 5 / 15 min
- [x] Démarrage via RESET + commande BLE
- [ ] Web interface (optionnelle)
- [ ] Tests longue durée / validation semelle
- [ ] Publication en open hardware

---

## Liens

- 📲 **Composant principal** : [XIAO nRF52840 Sense](https://wiki.seeedstudio.com/XIAO_BLE/)
- 📲 **Batterie** : [Batterie Lithium polymère 3,7v 800mAh](https://www.amazon.fr/401822-Liter-l%C3%A9lectronique-Rechargeable-t%C3%A9l%C3%A9phone/dp/B0892MS2D1)

## Dimensions

- XIAO nRF52840 Sense : W=21mm, H=? , L=17.5mm
- Batterie : W=24mm, H=4mm, L=18mm

## 📄 Licence

Projet sous licence **MIT**.  
> Libre d'utilisation, modification, redistribution – attribution requise.

---

## 📬 Contact

Pour toute question ou collaboration : 
