## 1. Définitions

### NAT (Network Address Translation)
- **Définition** : Traduction d'adresse IP source dans l'en-tête du paquet
- **Rôle** : Permet de convertir une adresse IP privée en adresse IP publique

### PAT (Port Address Translation)
- **Définition** : Traduction de l'adresse IP source + du port source
- **Rôle** : Permet à plusieurs machines d'utiliser **une seule** adresse IP publique via des ports différents
- **Alias** : NAT overload, NAT avec surcharge

---

## 2. Types de NAT

| Type | Mappage | Usage |
|------|---------|-------|
| **NAT Statique** | 1 IP privée ↔ 1 IP publique | Serveurs accessibles de l'extérieur |
| **NAT Dynamique** | n IP privées ↔ m IP publiques (pool) | Rotation d'adresses selon disponibilité |
| **PAT** | n IP privées ↔ 1 IP publique | Usage domestique/PME (économique) |

---

## 3. Terminologie NAT

| Terme | Définition |
|-------|------------|
| **Inside Local** | Adresse privée du périphérique interne (ex: 192.168.10.10) |
| **Inside Global** | Adresse publique traduite (ex: 209.165.200.226) |
| **Outside Local** | Adresse du destinataire vue du réseau interne |
| **Outside Global** | Adresse réelle du destinataire sur Internet |

---

## 4. Plages d'adresses privées (RFC 1918)

| Classe | Plage | CIDR |
|--------|-------|------|
| A | 10.0.0.0 - 10.255.255.255 | /8 |
| B | 172.16.0.0 - 172.31.255.255 | /12 |
| C | 192.168.0.0 - 192.168.255.255 | /16 |

> ⚠️ **Important** : Internet ne route pas les adresses privées !

---

## 5. Configuration des types de NAT

### 5.1 Préparation commune (Interfaces Inside/Outside)

```cisco
! Définir l'interface interne (réseau privé)
R1(config)# interface fa0/0
R1(config-if)# ip nat inside
R1(config-if)# exit

! Définir l'interface externe (réseau public)
R1(config)# interface s0/0
R1(config-if)# ip nat outside
R1(config-if)# exit

### 5.2 NAT Statique 

```cisco
! Syntaxe : ip nat inside source static [IP_locale] [IP_globale]
R1(config)# ip nat inside source static 192.168.1.100 201.49.10.30

! Exemple concret : serveur web interne accessible de l'extérieur
! Tout paquet destiné à 201.49.10.30 sera redirigé vers 192.168.1.100

### Vérification :

```cisco
  R1# show ip nat translations
Pro Inside global      Inside local       Outside local      Outside global
--- 201.49.10.30       192.168.1.100      ---                ---

### 5.3 NAT Dynamique

```cisco
! Étape 1 : Créer le pool d'adresses publiques
! Syntaxe : ip nat pool [nom] [IP_début] [IP_fin] netmask [masque]
R1(config)# ip nat pool POOL-NAT 201.49.10.17 201.49.10.30 netmask 255.255.255.240

! Étape 2 : Créer une ACL pour définir les adresses à traduire
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255
R1(config)# access-list 1 deny any

! Étape 3 : Lier l'ACL au pool
R1(config)# ip nat inside source list 1 pool POOL-NAT

### Vérification :

```cisco
R1# show ip nat translations
R1# show ip nat pool
R1# show ip nat statistics

### 5.4 PAT (NAT Overload)

#### Méthode A : Avec interface de sortie (recommandée pour une seule IP publique)

```cisco
! Étape 1 : ACL pour le réseau privé
R1(config)# access-list 2 permit 192.168.0.0 0.0.0.255

! Étape 2 : Configurer le PAT avec surcharge
! L'IP de l'interface s0/0 sera utilisée automatiquement
R1(config)# ip nat inside source list 2 interface serial 0/0 overload

#### Méthode B : Avec pool (si plusieurs IPs publiques mais surcharge activée)

```cisco
! Créer un pool avec une seule IP
R1(config)# ip nat pool PAT-POOL 201.49.10.17 201.49.10.17 netmask 255.255.255.240

! ACL
R1(config)# access-list 3 permit 192.168.0.0 0.0.0.255

! PAT avec pool + overload
R1(config)# ip nat inside source list 3 pool PAT-POOL overload

### Exemple de table de traduction PAT :

```cisco
R1# show ip nat translations
Pro Inside global          Inside local       Outside local      Outside global
tcp 201.49.10.17:4958      192.168.10.2:25412 130.25.2.23:80     130.25.2.23:80
tcp 201.49.10.17:5412      192.168.20.8:10584 130.25.2.23:80     130.25.2.23:80
tcp 201.49.10.17:5120      192.168.50.42:10152 130.25.2.23:80    130.25.2.23:80

## 6. Tableau récapitulatif des commandes

| Étape                   | NAT Statique                                   | NAT Dynamique                              | PAT                                                      |
| ----------------------- | ---------------------------------------------- | ------------------------------------------ | -------------------------------------------------------- |
| **Interfaces**          | `ip nat inside` / `ip nat outside`             | Identique                                  | Identique                                                |
| **Pool**                | Non                                            | `ip nat pool ...`                          | Optionnel                                                |
| **ACL**                 | Non                                            | `access-list ...`                          | `access-list ...`                                        |
| **Commande principale** | `ip nat inside source static [local] [global]` | `ip nat inside source list [n] pool [nom]` | `ip nat inside source list [n] interface [int] overload` |
| **Mot clé**             | `static`                                       | `pool`                                     | `overload`                                               |

## 7. Commandes de vérification et dépannage

| Commande                     | Description                                              |
| ---------------------------- | -------------------------------------------------------- |
| `show ip nat translations`   | Affiche la table de traduction active                    |
| `show ip nat statistics`     | Affiche les statistiques NAT (hits, misses, etc.)        |
| `show ip nat pool`           | Affiche les pools configurés                             |
| `show ip interface brief`    | Vérifie les IPs configurées sur les interfaces           |
| `clear ip nat translation *` | Vide la table de traduction (attention !)                |
| `debug ip nat`               | Active le debug en temps réel (à désactiver après usage) |
| `no debug ip nat`            | Désactive le debug NAT                                   |

## 8. Avantages et Inconvénients

### Avantages

| Avantage                 | Description                                   |
| ------------------------ | --------------------------------------------- |
| Économie d'adresses IPv4 | Préserve les adresses publiques limitées      |
| Sécurité                 | Masque la topologie du réseau interne         |
| Souplesse                | Changement de FAI sans renumérotation interne |
| Indépendance             | Adressage local indépendant du fournisseur    |

### Inconvénients

| Inconvénient                         | Description                                    |
| ------------------------------------ | ---------------------------------------------- |
| Dégradation des performances         | Le routeur doit examiner chaque paquet         |
| Perte de l'adressage de bout en bout | Problèmes avec certains protocoles (FTP, VoIP) |
| Perte de traçabilité                 | Dépannage plus difficile                       |
| Complexité                           | Nécessite une configuration et une maintenance |

## 9. Schéma de fonctionnement PAT

┌─────────────────┐         ┌──────────────────┐         ┌─────────────────┐
│  Réseau Privé   │         │     Routeur      │         │   Internet      │
│                 │         │   (Translation)  │         │                 │
│ 192.168.10.2    │────────→│  20.21.22.23     │────────→│  130.25.2.23    │
│ :25412          │         │ :4958            │         │  :80            │
│                 │         │                  │         │                 │
│ 192.168.20.8    │────────→│  20.21.22.23     │────────→│  130.25.2.23    │
│ :10584          │         │ :5412            │         │  :80            │
│                 │         │                  │         │                 │
│ 192.168.50.42   │────────→│  20.21.22.23     │────────→│  130.25.2.23    │
│ :10152          │         │ :5120            │         │  :80            │
└─────────────────┘         └──────────────────┘         └─────────────────┘
       ↑                              ↑                          ↑
   Inside Local                 Inside Global                Outside Global
   (IP:Port privés)            (IP publique + ports         (Destination
                               dynamiques)                   finale)

By tedell
