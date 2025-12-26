# 💻 Firmware (Code BA)

Le firmware du module SC941C est conçu pour un microcontrôleur PIC (Microchip), compilé probablement avec MPLAB et le compilateur Hi-Tech C ou XC8. Il gère les entrées/sorties locales et communique avec le reste du système Atrium via un bus I2C.

## Informations Générales

*   **Dossier sources** : `SC941C/Prog/code_ba (fichiers sources 1.7)/`
*   **Version** : 1.7
*   **Architecture** : Boucle principale + Interruptions
*   **Communication** : Esclave I2C

## Structure du Projet

Le code est organisé de manière modulaire dans le dossier `source/`.

| Fichier | Description |
| :--- | :--- |
| `main.c` | Point d'entrée. Initialisation et boucle principale (`while(1)`). |
| `hard.h` / `.c` | Abstraction matérielle (assignation des pins, init MCU). Définit l'adresse I2C (ex: `0x12` pour Chambres). |
| `gestionentrees.c` | Gestion des entrées boutons (anti-rebond, appui long, double clic). |
| `gestionsorties.c` | Pilotage des relais bistables et monostables. |
| `variateur.c` | Gestion des dimmers (conseigne d'éclairage, rampes). |
| `slavenode.c` | Gestion du protocole de communication (probablement la couche applicative slave). |
| `struct.h` | Définition des structures de données (états entrées/sorties). |
| `constantes.h` | Constantes globales et paramètres. |

## Fonctionnement Interne

### Boucle Principale
Le système fonctionne sur une boucle infinie qui appelle séquentiellement les tâches de gestion :
1.  **Watchdog** : Reset du chien de garde.
2.  **Traitement I2C** : Gestion des messages reçus (`vd_Traitement_I2C`).
3.  **Gestion Entrées** : Lecture et filtrage des états.
4.  **Logique Applicative** : Mapping entrées -> sorties (si autonome) ou attente ordres I2C.
5.  **Gestion Sorties** : Mise à jour physique des relais/triacs.

### Interruptions
*   **Timer** : Utilisé pour l'anti-rebond des entrées et les temporisations (clignotements, rampes variateur).
*   **I2C (SSP)** : Réception des trames de commande du maître Atrium.

## Structures de Données Clés

### Entrées (`struct_EntreeVariateur`)
Permet de distinguer plusieurs types d'interactions :
*   Appui simple
*   Appui long (gradation)
*   Double clic

### EEPROM
Le mapping EEPROM (`main.c`) stocke :
*   Configuration des types de sorties (Lampe, Variateur, Volet).
*   États sauvegardés (pour restauration après coupure courant).
*   Temps d'action (volets) et d'extinction.

## Compilation

Le projet utilise un `Makefile` situé dans `essensys_ba.X/`. Il est recommandé d'utiliser **MPLAB X IDE** pour ouvrir et compiler ce projet.
