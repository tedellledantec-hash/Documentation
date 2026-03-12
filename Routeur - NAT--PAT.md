# NAT & PAT - Résumé du cours

## 1. Définitions

### NAT (Network Address Translation)
- **Définition** : Traduction d'adresse IP source dans l'en-tête du paquet
- **Rôle** : Permet de convertir une adresse IP privée en adresse IP publique
- **Commandes de base** :
  ```cisco
  ! Définir les interfaces inside/outside
  R1(config)# interface fa0/0
  R1(config-if)# ip nat inside
  
  R1(config)# interface s0/0
  R1(config-if)# ip nat outside


### PAT (Port Address Translation)
- **Définition** : Traduction de l'adresse IP source + du port source
- **Rôle** : Permet à plusieurs machines d'utiliser une seule adresse IP publique via des ports différents
- **Alias** : NAT overload, NAT avec surcharge



## 2. Types de NAT

| Type              | Mappage                              | Usage                                   | Commande clé                                                  |
| ----------------- | ------------------------------------ | --------------------------------------- | ------------------------------------------------------------- |
| **NAT Statique**  | 1 IP privée ↔ 1 IP publique          | Serveurs accessibles de l'extérieur     | `ip nat inside source static [local] [global]`                |
| **NAT Dynamique** | n IP privées ↔ m IP publiques (pool) | Rotation d'adresses selon disponibilité | `ip nat inside source list [ACL] pool [nom]`                  |
| **PAT**           | n IP privées ↔ 1 IP publique         | Usage domestique/PME (économique)       | `ip nat inside source list [ACL] interface [sortie] overload` |


# Exemples de commandes

## NAT Statique

! Rediriger tout ce qui arrive sur 201.49.10.30 vers 192.168.1.100
R1(config)# ip nat inside source static 192.168.1.100 201.49.10.30

! Vérification
R1# show ip nat translations

### 1. NAT Statique conf détaillés :

! Étape 1 : Définir les interfaces
R1(config)# interface fa0/0
R1(config-if)# ip nat inside
R1(config-if)# exit

R1(config)# interface s0/0
R1(config-if)# ip nat outside
R1(config-if)# exit

! Étape 2 : Configurer la traduction statique
! Syntaxe : ip nat inside source static [IP_locale] [IP_globale]
R1(config)# ip nat inside source static 192.168.1.100 201.49.10.30

! Vérification
R1# show ip nat translations
R1# show ip nat statistics



## NAT Dynamique (avec pool)

! Créer le pool d'adresses publiques
R1(config)# ip nat pool POOL-NAT 201.49.10.17 201.49.10.30 netmask 255.255.255.240

! ACL pour définir qui peut être traduit
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255

! Application
R1(config)# ip nat inside source list 1 pool POOL-NAT


### NAT Dynamique conf détaillées :

! Étape 1 : Définir les interfaces
R1(config)# interface fa0/0
R1(config-if)# ip nat inside
R1(config-if)# exit

R1(config)# interface s0/0
R1(config-if)# ip nat outside
R1(config-if)# exit

! Étape 2 : Créer le pool d'adresses publiques
! Syntaxe : ip nat pool [nom] [début] [fin] netmask [masque]
R1(config)# ip nat pool POOL-NAT 201.49.10.17 201.49.10.30 netmask 255.255.255.240

! Étape 3 : Créer une ACL pour les adresses à traduire
R1(config)# access-list 1 permit 192.168.1.0 0.0.0.255
R1(config)# access-list 1 deny any

! Étape 4 : Lier l'ACL au pool
R1(config)# ip nat inside source list 1 pool POOL-NAT

! Vérification
R1# show ip nat translations
R1# show ip nat statistics
R1# show ip nat pool



### PAT (le plus courant)

! ACL pour le réseau privé
R1(config)# access-list 2 permit 192.168.0.0 0.0.0.255

! PAT sur l'interface de sortie
R1(config)# ip nat inside source list 2 interface serial 0/0 overload

### 3. PAT (NAT Overload) conf Détaillées :

#### méthode 1 : Avec une interface de sortie (la plus courante)

! Étape 1 : Définir les interfaces
R1(config)# interface fa0/0
R1(config-if)# ip nat inside
R1(config-if)# exit

R1(config)# interface s0/0
R1(config-if)# ip nat outside
R1(config-if)# exit

! Étape 2 : ACL pour le réseau privé
R1(config)# access-list 2 permit 192.168.0.0 0.0.0.255

! Étape 3 : Configurer le PAT avec surcharge (overload)
! L'IP de l'interface s0/0 sera utilisée
R1(config)# ip nat inside source list 2 interface serial 0/0 overload

! Vérification
R1# show ip nat translations
R1# show ip nat statistics


#### méthode 2 :

! Créer le pool (même si on utilise overload)
R1(config)# ip nat pool PAT-POOL 201.49.10.17 201.49.10.17 netmask 255.255.255.240

! ACL
R1(config)# access-list 3 permit 192.168.0.0 0.0.0.255

! PAT avec pool + overload
R1(config)# ip nat inside source list 3 pool PAT-POOL overload


### Commandes de vérification essentielles :

! Voir la table de traduction active
R1# show ip nat translations

! Voir les statistiques NAT
R1# show ip nat statistics

! Voir les pools configurés
R1# show ip nat pool

! Vider la table de traduction (utile pour dépannage)
R1# clear ip nat translation *

! Debug en temps réel
R1# debug ip nat


## 3. Terminologie NAT

| Terme              | Définition                                                 |
| ------------------ | ---------------------------------------------------------- |
| **Inside Local**   | Adresse privée du périphérique interne (ex: 192.168.10.10) |
| **Inside Global**  | Adresse publique traduite (ex: 209.165.200.226)            |
| **Outside Local**  | Adresse du destinataire vue du réseau interne              |
| **Outside Global** | Adresse réelle du destinataire sur Internet                |

## 4. Plages d'adresses privées 

| Classe | Plage                         | CIDR |
| ------ | ----------------------------- | ---- |
| A      | 10.0.0.0 - 10.255.255.255     | /8   |
| B      | 172.16.0.0 - 172.31.255.255   | /12  |
| C      | 192.168.0.0 - 192.168.255.255 | /16  |
