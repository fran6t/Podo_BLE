# 👣 Enregistreur de pas autonome BLE (Seeed XIAO BLE Sense)

[![Licence MIT](https://img.shields.io/badge/Licence-MIT-green.svg)](#licence)

## 🎯 Objectif

Créer un dispositif **compact, autonome et discret** à insérer dans une semelle de chaussure pour **surveiller l’activité de marche** d’un utilisateur, incluant :

- Activité de jour (~4 h de marche)
- Détection de mouvements nocturnes (lever, toilette)
- Transfert sans fil des données (BLE)
- Autonomie minimale de 14 jours

---

## 🧠 Fonctionnement général

Le système s'appuie sur le **capteur IMU LSM6DS3TR-C** du **Seeed XIAO BLE Sense**, qui intègre un **compteur de pas matériel** permettant un fonctionnement ultra-basse consommation.

- ⏱️ **Une mesure du nombre total de pas est enregistrée toutes les 5 minutes**
- 📅 L’utilisateur **envoie une commande de démarrage** via BLE (via l’app nRF Connect), qui injecte également l’heure de départ
- 🗃️ Les pas sont stockés en mémoire flash interne (aucune carte SD nécessaire)
- 📲 À la demande, une commande `DUMP` via BLE provoque le **transfert des données vers le smartphone**
- 🔋 Le tout est optimisé pour fonctionner **plus de 14 jours sur une batterie LiPo de 800 mAh**

---

## ⚙️ Architecture matérielle

| Composant                     | Description                                  |
|------------------------------|----------------------------------------------|
| Seeed Studio XIAO BLE Sense  | Microcontrôleur nRF52840 + IMU intégré       |
| Batterie LiPo 3.7 V ≥ 800 mAh| Autonomie ≥ 14 jours                         |
| Connexion BLE (nRF Connect)  | Pour la configuration et l’extraction        |
| Micro-USB-C                  | Recharge de la batterie intégrée             |

---

## 📦 Flux de données

### 1. Initialisation (via BLE)
- L’utilisateur connecte son téléphone avec **nRF Connect**
- Il envoie une commande `START yyyy-mm-ddThh:mm:ssZ`
- Le firmware initialise le compteur de pas et enregistre le temps de référence

### 2. Fonctionnement autonome
- Toutes les **5 minutes**, le firmware :
  - Lit le compteur de pas interne (LSM6DS3)
  - Enregistre la valeur si elle a changé
  - Associe la valeur à un timestamp relatif

### 3. Dump des données
- L’utilisateur envoie une commande `DUMP` via BLE
- Le système envoie les données via BLE Notifications (20 octets/paquet)
- Format : `[timestamp offset (2 octets)] + [compteur de pas (2 octets)]`

---

## 📱 Interaction via BLE

| Caractéristique     | Type    | UUID (exemple)           | Description                           |
|---------------------|---------|--------------------------|----------------------------------------|
| Commande            | Write   | `1234...`                | `START`, `DUMP`, `CLEAR`, etc.         |
| Données de pas      | Notify  | `5678...`                | Envoie les blocs horodatés             |
| Niveau batterie     | Read    | `0x2A19` (standard)      | % de batterie via ADC interne          |

Utilisez **nRF Connect (iOS/Android)** pour piloter la carte.

---

## 🔋 Autonomie & batterie

### 🔢 Consommation estimée

| Composant / Mode             | Consommation | Temps / jour | Total / jour |
|-----------------------------|--------------|--------------|--------------|
| Acquisition + mémoire       | 10 mA        | 4 h          | 40 mAh       |
| BLE publicité (1 Hz)        | 0.3 mA       | 10 h         | 3 mAh        |
| Veille RTC                  | ~2 µA        | 10 h         | 0.02 mAh     |
| **Total journalier**        |              |              | **~43 mAh**  |

### 🔋 Batterie recommandée : **800 mAh**
> Permet une autonomie de **≥ 16 jours** avec marge de sécurité.

---

## 🗃️ Stockage

| Format d’enregistrement       | Valeur           |
|-------------------------------|------------------|
| Fréquence d’enregistrement    | 1 point / 5 min  |
| Taille par point              | 4 octets         |
| Points par jour               | 288              |
| Taille par jour               | ~1.1 Ko          |
| 14 jours                      | ~15 Ko           |
| Capacité flash dispo          | ≥ 512 Ko (interne) |

---

## 🧩 Position et fiabilité

- Le compteur de pas **fonctionne quelle que soit l’orientation** de la carte
- Il suffit que la carte soit **fermement fixée** dans la semelle pour éviter les fausses détections dues aux vibrations
- Testée pour détecter même des mouvements **nocturnes faibles** (ex : lever la nuit)

---

## 🔒 Sécurité & robustesse

- Publicité BLE uniquement active **pendant les heures d’activité** (ex : 8h–18h, toutes les 5 min)
- Pas de données perdues même sans connexion mobile
- Possibilité d’injection de l'heure exacte par BLE à tout moment (resync)

---

## 🛠️ Outils recommandés

- 🔧 **IDE** : Arduino IDE + Seeed nRF52 Board package
- 📲 **App mobile** : [nRF Connect](https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-mobile) (iOS / Android)
- 📊 **Export CSV/JSON** : depuis nRF Connect, intégration possible avec Excel ou Python/Plotly
- 🖼️ **Modélisation 3D** (boîtier) : FreeCAD

---

## 🗺️ Roadmap

- [x] Lecture du compteur de pas matériel
- [x] Stockage horodaté toutes les 5 minutes
- [x] Communication BLE via GATT
- [ ] Création d’une WebApp ou interface front-end
- [ ] Validation en conditions réelles
- [ ] Publication open hardware

---

## 📄 Licence

Projet sous licence **MIT**.  
> Vous pouvez l’utiliser librement, le modifier, le redistribuer – avec attribution.

---

## 📬 Contact

Pour toute question ou suggestion : [votre.email@exemple.com]


