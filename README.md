<div align="center">

# Device Explorer

**Une application web mobile pour explorer et tester les Device API du smartphone**
Géolocalisation · Caméra · Orientation

![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-F7DF1E?logo=javascript&logoColor=black)
![Android](https://img.shields.io/badge/Android-WebView-3DDC84?logo=android&logoColor=white)
![Java](https://img.shields.io/badge/Java-ED8B00?logo=openjdk&logoColor=white)
![License](https://img.shields.io/badge/Licence-MIT-blue)

[**🚀 Démo en ligne**](https://asmaaaa01ab.github.io/mobile-device-explorer/) ·
[Fonctionnalités](#-fonctionnalités) ·
[Installation](#-installation) ·
[Architecture](#-architecture)

</div>

---

## 📖 Table des matières

1. [Contexte du projet](#-contexte-du-projet)
2. [Objectifs et intérêt](#-objectifs-et-intérêt)
3. [Aperçu](#-aperçu)
4. [Fonctionnalités](#-fonctionnalités)
5. [Les Device API utilisées](#-les-device-api-utilisées)
6. [Architecture](#-architecture)
7. [Structure du dépôt](#-structure-du-dépôt)
8. [Installation](#-installation)
9. [Gestion des permissions et des erreurs](#-gestion-des-permissions-et-des-erreurs)
10. [Compatibilité](#-compatibilité)
11. [Limites connues](#-limites-connues)
12. [Pistes d'amélioration](#-pistes-damélioration)
13. [Auteur](#-auteur)
14. [Licence](#-licence)

---

## 🎓 Contexte du projet

Ce projet accompagne une présentation intitulée **« Le Web mobile et les Device API »**. Il constitue la **partie pratique**.

Les smartphones embarquent de nombreux capteurs (GPS, caméra, gyroscope, accéléromètre…). Historiquement, y accéder demandait de développer une application **native**. Aujourd'hui, les navigateurs modernes exposent ce matériel à travers des **API Web standardisées** : une simple page HTML/JavaScript peut lire la position, ouvrir la caméra ou suivre l'inclinaison de l'appareil.

**Device Explorer** démontre concrètement cette évolution : une seule interface web, testable dans un navigateur mobile **et** intégrable dans une application Android.

## 🎯 Objectifs et intérêt

| Objectif | Description |
|---|---|
| **Démontrer** | Montrer que le web mobile peut accéder au matériel sans code natif. |
| **Comparer** | Présenter deux modes d'exécution : *web pur* (navigateur) et *hybride* (Android WebView). |
| **Sensibiliser** | Illustrer le modèle de permissions et les contraintes de sécurité (HTTPS obligatoire, consentement de l'utilisateur). |
| **Fiabiliser** | Montrer comment gérer proprement les refus de permission, les erreurs matérielles et l'incompatibilité. |

## 🖼️ Aperçu

<div align="center">

| Accueil | Position | Caméra | Orientation |
|:---:|:---:|:---:|:---:|
| ![Accueil](docs/screenshots/home.png) | ![Position](docs/screenshots/geo.png) | ![Caméra](docs/screenshots/camera.png) | ![Orientation](docs/screenshots/orientation.png) |

</div>

## ✨ Fonctionnalités

- 🧭 **Navigation type application mobile** : barre supérieure, navigation en bas, écrans séparés.
- 📍 **Géolocalisation** : latitude, longitude, précision (jauge à 3 niveaux), altitude, vitesse, suivi en continu.
- 🎥 **Caméra** : aperçu vidéo en direct, bascule caméra avant / arrière, affichage de la résolution.
- 🔄 **Orientation** : valeurs `alpha`, `beta`, `gamma` en temps réel, téléphone 3D et boussole animés.
- 🛡️ **Gestion d'erreurs claire** : bannières explicatives pour chaque cas (refus, indisponibilité, délai dépassé, matériel absent).
- 📊 **Tableau de bord** : état de compatibilité de chaque API et journal d'activité récente.
- ♿ **Accessibilité et UX** : zones tactiles ≥ 48 px, zones live pour lecteurs d'écran, `prefers-reduced-motion` respecté.
- ⚡ **Zéro dépendance** : un seul fichier `index.html`, sans framework ni bibliothèque externe.

## 🔌 Les Device API utilisées

| API | Interface principale | Usage dans le projet |
|---|---|---|
| **Geolocation API** | `navigator.geolocation` (`getCurrentPosition`, `watchPosition`) | Position GPS ponctuelle et suivi continu |
| **MediaDevices API** | `navigator.mediaDevices.getUserMedia()` | Flux vidéo de la caméra dans un élément `<video>` |
| **DeviceOrientation API** | événement `deviceorientation` | Inclinaison et rotation du téléphone |

> ⚠️ Les API de géolocalisation et de caméra exigent un **contexte sécurisé** (HTTPS ou `localhost`).

## 🏗️ Architecture

Le même fichier `index.html` peut être exécuté de deux manières :

```
                    ┌────────────────────────┐
                    │       index.html       │
                    │   HTML + CSS + JS      │
                    └───────────┬────────────┘
                                │
              ┌─────────────────┴─────────────────┐
              ▼                                   ▼
   ┌────────────────────┐              ┌──────────────────────────┐
   │   Mode WEB         │              │   Mode HYBRIDE           │
   │                    │              │                          │
   │  GitHub Pages      │              │  Application Android     │
   │  (HTTPS)           │              │  Activity + WebView      │
   │        │           │              │        │                 │
   │  Chrome / Safari   │              │  WebViewAssetLoader      │
   │  demande les       │              │  (HTTPS virtuel)         │
   │  permissions       │              │  WebChromeClient gère    │
   │                    │              │  les permissions Android │
   └────────────────────┘              └──────────────────────────┘
```

### Mode hybride : points techniques clés

- **`WebViewAssetLoader`** (`androidx.webkit`) sert les fichiers du dossier `assets/` sur une origine HTTPS virtuelle (`https://appassets.androidplatform.net`). Sans cela, `file://` n'est pas un contexte sécurisé et `getUserMedia` est indisponible.
- **`WebChromeClient.onPermissionRequest`** relaie la demande de caméra de la page vers la permission Android `CAMERA`.
- **`WebChromeClient.onGeolocationPermissionsShowPrompt`** relaie la demande de position vers `ACCESS_FINE_LOCATION`.
- Seule l'**origine locale de confiance** peut obtenir ces autorisations.
- Les permissions sont demandées **à l'exécution**, au moment où l'utilisateur déclenche la fonctionnalité.

## 📂 Structure du dépôt

```
device-explorer/
├── index.html                  # Application web (servie par GitHub Pages)
├── README.md
├── docs/
│   └── screenshots/            # Captures d'écran utilisées dans ce README
└── android/                    # Projet Android Studio (mode hybride)
    └── app/src/main/
        ├── AndroidManifest.xml
        ├── assets/
        │   └── index.html      # Copie de la page web
        ├── java/com/example/mobiledeviceexplorer/
        │   └── MainActivity.java
        └── res/layout/
            └── activity_main.xml
```

## 🚀 Installation

### Option 1 : version web (la plus simple)

1. Ouvrez la [démo en ligne](https://asmaaaa01ab.github.io/mobile-device-explorer/) dans **Chrome sur Android**.
2. Autorisez la position et la caméra lorsque le navigateur le demande.
3. *(Facultatif)* Menu ⋮ › **Ajouter à l'écran d'accueil** pour l'utiliser comme une application.

### Option 2 : exécuter en local

```bash
git clone https://asmaaaa01ab.github.io/mobile-device-explorer.git
cd device-explorer

# Serveur local (localhost est considéré comme contexte sécurisé)
python -m http.server 8000
```

Ouvrez ensuite `http://localhost:8000`. Pour tester sur un téléphone, utilisez un tunnel HTTPS ou la version hébergée, car une adresse IP locale en `http://` n'est pas sécurisée.

### Option 3 : application Android (Android Studio)

**Prérequis** : Android Studio récent, SDK Android, minSdk 24 ou plus.

1. Ouvrez le dossier `android/` dans Android Studio.
2. Vérifiez la dépendance dans `app/build.gradle(.kts)` :
   ```kotlin
   implementation("androidx.webkit:webkit:1.12.1")
   ```
3. Cliquez sur **Sync Now**, puis ▶️ **Run** sur un appareil réel (recommandé) ou un émulateur.
4. Pour l'émulateur, simulez le matériel via *Extended controls* (Location, Virtual sensors, Camera).

**Permissions déclarées dans le manifeste** :

| Permission | Rôle |
|---|---|
| `INTERNET` | Chargement éventuel de la version hébergée |
| `ACCESS_FINE_LOCATION` / `ACCESS_COARSE_LOCATION` | Géolocalisation |
| `CAMERA` | Aperçu vidéo |

> L'API DeviceOrientation ne nécessite aucune permission Android.

## 🛡️ Gestion des permissions et des erreurs

L'application anticipe les principaux scénarios d'échec et affiche un message explicite avec une action corrective.

| API | Situation | Réaction de l'interface |
|---|---|---|
| Geolocation | Permission refusée (code 1) | Explique comment réactiver l'autorisation dans les paramètres |
| Geolocation | Position indisponible (code 2) | Invite à activer le GPS / vérifier le signal |
| Geolocation | Délai dépassé (code 3) | Propose de réessayer, de préférence en extérieur |
| Caméra | `NotAllowedError` | Indique comment autoriser la caméra |
| Caméra | `NotFoundError` | Informe qu'aucune caméra n'est détectée |
| Caméra | `NotReadableError` | Signale que la caméra est utilisée par une autre application |
| Caméra | `OverconstrainedError` | Signale qu'aucune caméra ne correspond aux réglages |
| Orientation | Aucun événement après 3 s | Indique capteurs absents ou désactivés |
| Toutes | API non supportée / contexte non sécurisé | Badge « Non supporté » et message dédié |

## 🌐 Compatibilité

| Environnement | Géolocalisation | Caméra | Orientation |
|---|:---:|:---:|:---:|
| Chrome Android | ✅ | ✅ | ✅ |
| Android WebView (app) | ✅ | ✅ | ✅ |
| Safari iOS | ✅ | ✅ | ⚠️ Demande d'autorisation explicite |
| Navigateur de bureau | ✅ | ✅ (webcam) | ❌ Pas de capteurs de mouvement |

## ⚠️ Limites connues

- Le HTTPS est **obligatoire** pour la caméra et la géolocalisation.
- La précision GPS dépend de l'appareil, de la réception et des réglages de localisation.
- Sur émulateur, les capteurs et la caméra doivent être simulés manuellement.
- La caméra est volontairement arrêtée quand la page passe en arrière-plan.
- Aucune donnée n'est enregistrée ni transmise : tout reste sur l'appareil.

## 🔭 Pistes d'amélioration

- [ ] API supplémentaires : Vibration, Battery Status, Network Information, Web Share
- [ ] Capture de photo depuis l'aperçu caméra
- [ ] Affichage de la position sur une carte
- [ ] Transformation en PWA (manifeste + service worker) pour un usage hors-ligne
- [ ] Pont JavaScript ↔ Java (`@JavascriptInterface`) pour des fonctions natives
- [ ] Tests automatisés

## 👤 Auteur

**Asmae Abakouy** — Élève ingénieure en Génie Logiciel, ENSA Al Hoceima
[GitHub](https://github.com/asmaaaa01ab)

**Fatima Zohra Aalilouch** et **Zineb Salihi** — Élèves ingénieures en Génie Logiciel, ENSA Al Hoceima

*Projet réalisé en groupe dans le cadre du module **Développement des applications mobiles**, comme démonstration pratique lors d'une présentation sur le thème **« Le Web mobile et les Device API (capteurs, …) »**.*

---

<div align="center">

Si ce projet vous a été utile, pensez à lui laisser une ⭐

</div>
