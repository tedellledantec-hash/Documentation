# Le routage dynamique
*BTS SIO – B2*

---

## Sommaire

1. [Rôle des protocoles de routage dynamique](#1-rôle-des-protocoles-de-routage-dynamique)
2. [Routage statique / routage dynamique](#2-routage-statique--routage-dynamique)
3. [Fonctionnement du routage dynamique](#3-fonctionnement-du-routage-dynamique)
4. [Classification](#4-classification-des-protocoles-de-routage)
5. [Classful / Classless](#5-classful--classless)
6. [La convergence d'un réseau](#6-la-convergence-dun-réseau)
7. [Le Métrique](#7-meilleur-chemin-et-métrique)
8. [Vecteur de distance ou états de liens](#8-vecteur-de-distance-ou-état-de-liens)

---

## 1. Rôle des protocoles de routage dynamique

Les **protocoles de routage dynamique** sont utilisés par les routeurs pour partager des informations sur l'accessibilité et l'état des réseaux distants. Ils effectuent plusieurs tâches, notamment :

- **Détection de réseaux**
- **Mise à jour et maintenance des tables de routage**

### Principe du routage dynamique

Chaque routeur annonce les réseaux auxquels il est connecté à ses voisins. Ces informations se propagent progressivement à travers le réseau jusqu'à ce que tous les routeurs aient connaissance de l'ensemble des réseaux.

> **Exemple :** Routeur 2 annonce : *"Je suis connecté à un nouveau réseau C, passe par moi si tu veux le joindre !"*

Les protocoles de routage utilisent trois composants principaux :

| Composant | Description |
|-----------|-------------|
| **Structures de données** | Tables créées et gérées par le protocole (ex : table de voisinage, table topologique) |
| **Messages de protocole** | Messages échangés entre routeurs (Hello, mise à jour, requête, réponse…) |
| **Algorithmes de routage** | Calculent les meilleures routes (ex : algorithme DUAL pour EIGRP) |

---

## 2. Routage statique / routage dynamique

### Avantages et inconvénients du routage dynamique

| Avantages | Inconvénients |
|-----------|---------------|
| Approprié pour toutes les topologies où plusieurs routeurs sont requis | Peut être plus complexe à mettre en œuvre |
| Généralement indépendant de la taille du réseau | Moins sécurisé – des paramètres de configuration supplémentaires sont nécessaires pour la sécurisation |
| Adapte automatiquement la topologie pour réacheminer le trafic si possible | La route dépend de la topologie en cours |
| | Nécessite des capacités supplémentaires en matière de processeur, de mémoire vive et de bande passante |

### Comparaison statique / dynamique

| Critère | Routage statique | Routage dynamique |
|---------|:---:|:---:|
| Convient à plusieurs topologies de routeur | ✓ | ✓ |
| S'adapte aux modifications topologiques pour réacheminer le trafic | ✗ | ✓ |
| Facile à implémenter dans un petit réseau | ✓ | ✗ |
| Nécessite des capacités supplémentaires (CPU, RAM, bande passante) | ✗ | ✓ |
| La route menant à la destination est toujours la même | ✓ | ✗ |

---

## 3. Fonctionnement du routage dynamique

De manière générale, le fonctionnement d'un protocole de routage dynamique se déroule ainsi :

1. Le routeur **envoie et reçoit** des messages de routage sur ses interfaces.
2. Le routeur **partage** les messages et informations de routage avec d'autres routeurs utilisant le même protocole.
3. Les routeurs **échangent** des informations pour découvrir des réseaux distants.
4. Lorsqu'un routeur détecte une **modification de topologie**, le protocole peut l'annoncer aux autres routeurs.

### Exemple d'évolution des tables de routage

**Au démarrage** (chaque routeur ne connaît que ses réseaux directement connectés) :

```
Topologie : R1 -- R2 -- R3
Réseaux  : 10.1.0.0  10.2.0.0  10.3.0.0  10.4.0.0

R1 : { 10.1.0.0/Fa0/0/0 saut, 10.2.0.0/S0/0/0 saut }
R2 : { 10.2.0.0/S0/0/0 saut, 10.3.0.0/S0/1/0 saut }
R3 : { 10.3.0.0/S0/1/0 saut, 10.4.0.0/Fa0/0/0 saut }
```

**Après le premier échange** (chaque routeur découvre les réseaux de ses voisins) :

```
R1 ajoute : 10.3.0.0 via S0/0 (1 saut)
R2 ajoute : 10.1.0.0 via S0/0 (1 saut), 10.4.0.0 via S0/1 (1 saut)
R3 ajoute : 10.2.0.0 via S0/1 (1 saut)
```

**Après la mise à jour suivante** (convergence complète) :

```
R1 ajoute : 10.4.0.0 via S0/0 (2 sauts)
R2 : table complète
R3 ajoute : 10.1.0.0 via S0/1 (2 sauts)
```

---

## 4. Classification des protocoles de routage

### Systèmes autonomes (SA)

Un **système autonome (SA)** est un ensemble de routeurs sous une administration commune (ex : une entreprise). Deux types de protocoles existent :

- **IGP (Interior Gateway Protocol)** : routage *intra-SA*, au sein d'un même système autonome.
  - Exemples : **RIP, EIGRP, OSPF, IS-IS**
- **EGP (Exterior Gateway Protocol)** : routage *inter-SA*, entre différents systèmes autonomes.
  - Le seul protocole EGP viable aujourd'hui : **BGP** (Border Gateway Protocol), protocole officiel d'Internet.

### Arbre de classification

```
Protocoles de routage dynamique
├── IGP (Interior Gateway Protocols)
│   ├── Vecteur de distance
│   │   ├── RIPv1 → RIPv2
│   │   └── IGRP  → EIGRP
│   └── État de liens
│       ├── OSPF
│       └── IS-IS
└── EGP (Exterior Gateway Protocols)
    └── BGP
```

### Liste des protocoles

| Protocole | Signification |
|-----------|--------------|
| **RIP** | Routing Information Protocol |
| **IGRP** | Interior Gateway Routing Protocol |
| **EIGRP** | Enhanced Interior Gateway Routing Protocol |
| **OSPF** | Open Shortest Path First |
| **IS-IS** | Intermediate System-to-Intermediate System |
| **BGP** | Border Gateway Protocol |

---

## 5. Classful / Classless

### Protocoles Classful (avec classe)

Les protocoles **classful** n'envoient **pas le masque** lors des mises à jour de routage.

- Protocoles anciens : **RIPv1, IGRP**
- Conçus à l'époque où les adresses étaient classées (A, B, C)
- Le masque était déduit automatiquement du premier octet de l'adresse

| Classe | 1er octet | Masque par défaut |
|--------|-----------|-------------------|
| Classe A | 0.x.x.x – 127.x.x.x | 255.0.0.0 (/8) |
| Classe B | 128.x.x.x – 191.x.x.x | 255.255.0.0 (/16) |
| Classe C | 192.x.x.x – 223.x.x.x | 255.255.255.0 (/24) |

### Protocoles Classless (sans classe)

Les protocoles **classless** incluent le masque dans les mises à jour.

- Protocoles modernes : **RIPv2, EIGRP, OSPF, IS-IS, BGP**
- Supportent **VLSM** (Variable-Length Subnet Masking)
- Supportent **CIDR** (Classless Inter-Domain Routing)
- Supportent les réseaux non-contigus

> **Remarque :** Les protocoles de routage pour IPv6 sont **toujours classless**.

---

## 6. La convergence d'un réseau

La **convergence** est atteinte lorsque les tables de routage de tous les routeurs deviennent cohérentes et complètes. Un réseau n'est pas totalement opérationnel tant qu'il n'a pas convergé.

Le **temps de convergence** représente le temps nécessaire pour que les routeurs :
1. Partagent les informations de routage
2. Calculent les meilleures routes
3. Mettent à jour leurs tables de routage

### Vitesse de convergence par protocole

| Protocole | Vitesse de convergence |
|-----------|----------------------|
| **RIP** | 🐢 Lente |
| **EIGRP** | ⚡ Rapide |
| **OSPF** | ⚡ Rapide |
| **IS-IS** | ⚡ Rapide |

---

## 7. Meilleur chemin et métrique

Les **métriques** sont des valeurs utilisées pour comparer les routes et déterminer le **chemin préféré** vers une destination. Chaque protocole utilise ses propres métriques.

### Métriques par protocole

| Protocole | Métrique utilisée |
|-----------|------------------|
| **RIP** | Nombre de sauts (hop count) — max 15 |
| **EIGRP** | Débit, latence, fiabilité, charge |
| **OSPF** | Débit (bande passante) |

> **Exemple :** Entre deux chemins vers la même destination, RIP choisit celui avec le moins de sauts, tandis qu'OSPF choisit celui avec la plus grande bande passante.

---

## 8. Vecteur de distance ou état de liens

Les protocoles IGP se divisent en deux grandes familles :

### Protocoles à vecteur de distance

Les routes sont annoncées sous forme de **vecteur** (direction) et **distance** (métrique).

- La **distance** = métrique (nombre de sauts, bande passante…)
- La **direction** = adresse du prochain routeur ou interface de sortie
- Algorithme utilisé : **Bellman-Ford**

**Caractéristiques :**
- Ne connaît **pas la topologie complète** du réseau
- Sait uniquement ce que ses voisins lui ont communiqué
- Analogie : pancartes kilométriques sur une route

**Protocoles :** RIPv1, RIPv2, IGRP, EIGRP

### Protocoles à état de liens (Link-State)

Ces protocoles construisent une **vue complète** de la topologie du réseau.

- Équivalent à une **carte détaillée** du réseau
- Algorithme utilisé : **Shortest Path First (SPF)**
- Chaque routeur crée une carte topologique, puis calcule la meilleure route

**Avantage clé :** Pas de mises à jour régulières. Une mise à jour n'est envoyée qu'en cas de **modification de la topologie**.

**Protocoles :** OSPF, IS-IS

### Quand utiliser chaque type ?

| Situation | Vecteur de distance | État de liens |
|-----------|:---:|:---:|
| Réseau simple, sans hiérarchie | ✓ | |
| Administrateur peu expérimenté | ✓ | |
| Convergence rapide non critique | ✓ | |
| Réseau hiérarchique (grand réseau) | | ✓ |
| Convergence rapide primordiale | | ✓ |
| Administrateur expérimenté | | ✓ |

### Comparaison des protocoles

| Critère | RIPv1 | RIPv2 | IGRP | EIGRP | OSPF | IS-IS |
|---------|:-----:|:-----:|:----:|:-----:|:----:|:-----:|
| **Type** | Vecteur dist. | Vecteur dist. | Vecteur dist. | Vecteur dist. | État liens | État liens |
| **Vitesse de convergence** | Lente | Lente | Lente | Rapide | Rapide | Rapide |
| **Évolutivité** | Faible | Faible | Faible | Élevée | Élevée | Élevée |
| **Support VLSM** | Non | Oui | Non | Oui | Oui | Oui |
| **Utilisation des ressources** | Faible | Faible | Faible | Moyenne | Élevée | Élevée |
| **Implémentation** | Simple | Simple | Simple | Complexe | Complexe | Complexe |

---
  by tedell
