# La Sauvegarde des Données

> **BTS SIO**  
> **Cours sur la sauvegarde des données**

---

## Table des matières
1. [Définitions](#1-définitions)
2. [Pourquoi sauvegarder ?](#2-pourquoi-sauvegarder-)
3. [Les critères de choix](#3-les-critères-de-choix)
4. [Les différents types de sauvegarde](#4-les-différents-types-de-sauvegarde)
5. [Sauvegarde du client ou du serveur ?](#5-sauvegarde-du-client-ou-du-serveur-)
6. [Où sauvegarder ?](#6-où-sauvegarder-)
7. [Le bit d'archive](#7-le-bit-darchive)
8. [Après le crash : le DIMA, le PRA](#8-après-le-crash--le-dima-le-pra)

---

## 1) Définitions

### Sauvegarde (Backup)

> **La sauvegarde** est l'opération qui consiste à **dupliquer et mettre en sécurité** les données contenues dans un système informatique.

### Notions connexes

| Terme | Définition |
|-------|------------|
| **Enregistrement/Stockage** | Opération d'écriture des données sur un support durable (disque magnétique, SSD, clé USB, bandes magnétiques, etc.) à des fins de sécurité ou d'hébergement de la donnée vivante |
| **Archivage** | Enregistrement de données sur un support à des fins **légales ou historiques** |
| **Restauration** | Opération inverse de la sauvegarde : réutilisation des données sauvegardées |

---

## 2) Pourquoi sauvegarder ?

### Objectif principal
La sauvegarde des données **préserve l'activité de l'entreprise**, notamment en cas de défaillance du système informatique.

### Les risques menaçant les données

#### 1. Risques humains
- Perte ou vol d'appareil contenant des données d'entreprise
- Mauvaise manipulation entraînant l'effacement de données sensibles
- Piratage des données

#### 2. Risques liés à l'environnement
- Incendie dans les locaux
- Inondations et catastrophes naturelles

#### 3. Risques liés aux dysfonctionnements matériels
- Perte d'un serveur
- Défaillance de disques durs

### Les enjeux économiques

En cas de perte de données, l'impact financier peut être notable :
- Disparition de fichiers/applications sensibles (bases de données clients, rapports financiers)
- Perte de temps pour la remise en ligne des données

### Objectifs stratégiques

- **Sauvegarder son image de marque** auprès des clients et partenaires
- **Assurer une productivité constante** et maintenir le chiffre d'affaires
- **Éviter la perte d'informations sensibles** (ex : fichier client en cas de piratage)

### La règle du 3-2-1-1

#### **Règle fondamentale pour les données sensibles :**
-> 3 : Conserver 3 copies de vos fichiers
(original + 2 sauvegardes)
-> 2 : Sauvegarder sur 2 types de supports différents
(NAS, disques externes, cloud, bandes magnétiques...)
-> 1 : Conserver 1 copie hors-site
(dans un lieu différent de l'entreprise)
-> 1 : 1 lieu différent (déconnecté)
-> 0 : Aucun problème

---

## 3) Les critères de choix

### Critères techniques

| Critère | Description |
|---------|-------------|
| **Capacité de stockage** | Espace disponible sur le support |
| **Vitesse de sauvegarde** | Rapidité de l'opération de copie |
| **Fiabilité du support** | Durabilité après longue période de stockage |
| **Simplicité de classement** | Facilité d'organisation des sauvegardes |
| **Facilité de restauration** | Rapidité et simplicité pour récupérer les données |
| **Coût global** | Investissement matériel et logiciel |

### Critères physiques (pour grands systèmes)

- Volume physique des supports
- Poids
- Sensibilité à la température
- Sensibilité à l'humidité
- Sensibilité à la poussière
- Sensibilité à la lumière

### Fonctionnalités supplémentaires

- Possibilité de sélectionner les données à sauvegarder
- Compression des fichiers
- Chiffrement des données

---

## 4) Les différents types de sauvegarde

### Les trois grandes familles

- 1 : COMPLÈTE (Full Backup)  → Tous les fichiers et dossiers
- 2 : DIFFÉRENTIELLE  → Fichiers modifiés/ajoutés depuis la dernière - sauvegarde COMPLÈTE
- 3 :INCRÉMENTIELLE   → Fichiers modifiés/ajoutés depuis la dernière - sauvegarde (quelle que soit son type)

## 5) Sauvegarde du client ou du serveur ?

### Sauvegarde des postes clients 

> **Principe général :** Les données sur poste client sont réputées **moins importantes** que celles des systèmes centraux.

| Aspect | Détail |
|--------|--------|
| **Usage** | Principalement par les particuliers |
| **Stratégie entreprise** | Reste marginale |
| **Évolution** | La virtualisation des stations de travail rendra obsolète les outils classiques |

### 🖥️ Sauvegarde des serveurs

- Données critiques de l'entreprise
- Systèmes centralisés
- Stratégie de sauvegarde prioritaire

--- 

## 6) Où sauvegarder ?

### Option 1 : Poste client + Disque externe

**Description :** Sauvegarde programmée sur l'ordinateur + copie régulière sur support amovible (DVD, clé USB, disque dur externe)

| Avantages | Inconvénients |
|-------------|------------------|
| Mobilité (sauvegarde possible n'importe où) | Point de restauration ne garantit pas les données personnelles |
| | Copie manuelle chronophage |

### Option 2 : Serveur ou NAS

**Description :** Sauvegarde sur serveur équipé de nombreux disques ou sur NAS (Network Attached Storage)

| Avantages | Inconvénients |
|-------------|------------------|
| Sauvegardes automatiques et sécurisées | Pas toujours en temps réel (risque de perte de données) |
| Coûts matériels relativement faibles | Restauration nécessite des compétences techniques |

### Option 3 : En ligne (Cloud)

**Description :** Sauvegarde sur serveur distant via Internet, en léger décalé ou temps réel

| Avantages | Inconvénients |
|-------------|------------------|
| Sauvegarde quasi-synchrone (perte minimale) | Temps de récupération potentiellement long |
| Gestion simple (codes d'accès admin) | Impossible sans connexion Internet |
| Grand volume disponible (1000 Go+) | Coût d'abonnement parfois élevé |

> **Attention :** Il est fortement recommandé de **chiffrer ses données** avant envoi sur le cloud !

---

## 7) Le bit d'archive

### Définition

> Le **bit d'archive** (ou attribut d'archivage) est un **marqueur** positionné à "vrai" (1) lorsqu'un fichier est créé ou modifié.

**Signification :** *"Je viens d'être modifié ou créé : je suis prêt à être archivé"*

**Sous Windows :** Visible et modifiable via la commande `ATTRIB` (attribut `A` pour Archive)

### Fonctionnement selon les types de sauvegarde

| Type de sauvegarde | Action sur le bit d'archive | Comportement |
|-------------------|----------------------------|--------------|
| **Différentielle** | Archive les fichiers avec bit = 1 | **Ne repositionne pas** les attributs à 0 en fin d'exécution |
| **Incrémentielle** | Archive les fichiers avec bit = 1 | **Remet tous les attributs à 0** en fin de traitement |

---

## 8) Après le crash : le DIMA, le PRA

### Impact d'un désastre informatique

> **Statistique alarmante :**  
> **40% des entreprises** ayant subi un arrêt de **72 heures** de leurs moyens informatiques et télécoms **ne survivent pas** au désastre !

### ⏱️ Indicateurs clés : PDMA/RPO et DMIA/RTO

| Acronyme | Signification | Définition |
|----------|---------------|------------|
| **PDMA** (RPO) | *Perte de Données Maximale Admissible* | Durée maximale acceptable entre la dernière sauvegarde et l'incident. Quantifie la perte de données acceptée. |
| **DMIA** (RTO) | *Délai Maximal d'Interruption Admissible* | Temps total pour : décision de passage en mode secours + relance de la ressource + restauration des données |

### PCA : Plan de Continuité d'Activité

> **Définition officielle :**  
> Le PCA représente l'ensemble des mesures visant à assurer, selon divers scénarios de crises, y compris face à des chocs extrêmes, le maintien — le cas échéant de façon temporaire selon un mode dégradé — des prestations de services ou d'autres tâches opérationnelles essentielles ou importantes de l'entreprise, puis la reprise planifiée des activités.

### PRA : Plan de Reprise d'Activité

> **Définition :**  
> Le PRA (DR : *Disaster Recovery Plan*) définit **comment les données sauvegardées seront utilisées** pour reprendre l'activité après un incident.

**Principe :** Sauvegarder les données ne suffit pas, il faut aussi prévoir leur exploitation en cas de crise.

### Synthèse PCA vs PRA

| | PCA | PRA |
|---|-----|-----|
| **Objectif** | Maintenir l'activité pendant la crise | Reprendre l'activité après la crise |
| **Portée** | Continuité opérationnelle | Récupération des systèmes |
| **Durée** | Mode dégradé temporaire | Retour à la normale |
| **Focus** | Processus métier | Infrastructure IT et données |

---

  by Tedell
