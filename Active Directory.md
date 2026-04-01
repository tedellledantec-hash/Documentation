# 📘 Guide Complet : Administration Active Directory

## Table des matières

1. [Création d'un utilisateur et affectation à une UO](#1-création-dun-utilisateur-et-affectation-à-une-uo)
2. [Création d'une GPO et application aux PC](#2-création-dune-gpo-et-application-aux-pc)
3. [Création d'un pool DHCP avec étendue IP](#3-création-dun-pool-dhcp-avec-étendue-ip)
4. [Intégration d'un PC dans l'AD](#4-intégration-dun-pc-dans-lad-domain-join)

---

## 1. Création d'un utilisateur et affectation à une UO

### Étape 1.1 : Créer l'Unité d'Organisation (UO)

1. Ouvrir **Active Directory Users and Computers** (`dsa.msc`)
2. Clic droit sur le domaine → **New** → **Organizational Unit**
3. Nommer l'UO (ex : `Service_Informatique`)
4. Décocher *"Protect container from accidental deletion"* si nécessaire
5. Cliquer sur **OK**

### Étape 1.2 : Créer l'utilisateur

1. Clic droit sur l'UO créée → **New** → **User**
2. Remplir les informations :
   - **First name** : Prénom
   - **Last name** : Nom
   - **User logon name** : identifiant de connexion
3. Cliquer **Next**
4. Définir un mot de passe
5. Cocher *"User must change password at next logon"* (recommandé)
6. Cliquer **Next** puis **Finish**

### Étape 1.3 : Nommer l'utilisateur administrateur de l'UO

1. Clic droit sur l'UO → **Properties** → **Security** → **Advanced**
2. Cliquer **Add** → **Select a principal**
3. Rechercher l'utilisateur créé
4. Dans **Applies to** : choisir *"This object and all descendant objects"*
5. Cocher les permissions :
   - ✅ Create, delete, and manage user accounts
   - ✅ Create, delete, and manage groups
   - ✅ Modify the membership of a group
   - ✅ Read all properties
   - ✅ Write all properties
6. Cliquer **OK** à chaque fenêtre

> 💡 **Alternative via groupe** : Ajouter l'utilisateur au groupe `Account Operators` ou créer un groupe délégué personnalisé.

---

## 2. Création d'une GPO et application aux PC

### Étape 2.1 : Créer la GPO

1. Ouvrir **Group Policy Management** (`gpmc.msc`)
2. Développer le domaine → clic droit sur **Group Policy Objects** → **New**
3. Nommer la GPO (ex : `Config_PCs_Entreprise`)
4. Clic droit sur la GPO → **Edit**

### Étape 2.2 : Configurer les paramètres (exemples)

**Configuration Windows Update :**

```
Computer Configuration → Policies → Administrative Templates
→ Windows Components → Windows Update
→ "Configure Automatic Updates" → Enabled
```

**Restriction logicielle :**

```
Computer Configuration → Policies → Windows Settings
→ Security Settings → Software Restriction Policies
```

**Fond d'écran corporate :**

```
User Configuration → Policies → Administrative Templates
→ Desktop → Desktop → "Desktop Wallpaper"
```

### Étape 2.3 : Lier la GPO aux PC

**Option A : Lier au domaine (tous les PC)**

1. Clic droit sur le domaine → **Link an Existing GPO**
2. Sélectionner la GPO créée
3. Cliquer **OK**

**Option B : Lier à une OU spécifique**

1. Créer une OU `Ordinateurs_Entreprise`
2. Déplacer les PC dans cette OU
3. Clic droit sur l'OU → **Link an Existing GPO**

### Étape 2.4 : Vérifier l'application

```powershell
# Sur un PC client (en administrateur)
gpupdate /force
gpresult /r
```

---

## 3. Création d'un pool DHCP avec étendue IP

### Étape 3.1 : Installer le rôle DHCP

1. **Server Manager** → **Add roles and features**
2. Role-based → sélectionner le serveur
3. Cocher **DHCP Server**
4. Suivre l'assistant jusqu'à l'installation
5. Cliquer **Complete DHCP configuration** dans les notifications

### Étape 3.2 : Créer l'étendue (Scope)

1. Ouvrir **DHCP** (`dhcpmgmt.msc`)
2. Clic droit sur **IPv4** → **New Scope**
3. Configurer l'assistant :

| Étape | Configuration |
|---|---|
| Name | `Pool_Entreprise` |
| IP Range | `192.168.1.100` à `192.168.1.200` |
| Subnet mask | `255.255.255.0` |
| Exclusions | `192.168.1.150` - `192.168.1.160` (réservé) |
| Lease duration | 8 jours |
| Router | `192.168.1.1` |
| DNS | IP du contrôleur de domaine |
| Domain name | `votredomaine.local` |

4. Activer l'étendue : *"Yes, I want to activate this scope now"*

### Étape 3.3 : Configurer les options DHCP pour l'AD

**Option importante — DNS dynamique :**

Dans les propriétés **IPv4** → **DNS** :
- Activer *"Dynamically update DNS A and PTR records"*
- Cocher *"Dynamically update DNS A and PTR records for DHCP clients that do not request updates"*

### Étape 3.4 : Autoriser le serveur DHCP dans l'AD

```powershell
# En PowerShell (administrateur)
Add-DhcpServerInDC -DnsName "votreserveur.domaine.local" -IPAddress 192.168.1.10
```

---

## 4. Intégration d'un PC dans l'AD (Domain Join)

### Étape 4.1 : Prérequis réseau

Vérifier sur le PC client :

- ✅ Connecté au même réseau que le DC
- ✅ Ping vers le contrôleur de domaine fonctionnel
- ✅ DNS configuré vers le DC

```powershell
# Vérifier la résolution DNS
nslookup votredomaine.local
nslookup dc01.votredomaine.local
```

### Étape 4.2 : Rejoindre le domaine (GUI)

1. **Settings** → **System** → **About** → **Advanced system settings**
2. Onglet **Computer Name** → **Change**
3. Sélectionner **Domain** → saisir `votredomaine.local`
4. Entrer les credentials d'un compte ayant les droits (ex : `administrateur@votredomaine.local`)
5. Redémarrer le PC

### Étape 4.3 : Rejoindre le domaine (PowerShell — Recommandé)

```powershell
# Exécuter en administrateur
Add-Computer -DomainName "votredomaine.local" `
             -Credential "votredomaine.local\administrateur" `
             -Restart
```

Ou avec spécification de l'OU :

```powershell
Add-Computer -DomainName "votredomaine.local" `
             -OUPath "OU=Ordinateurs_Entreprise,DC=votredomaine,DC=local" `
             -Credential "votredomaine.local\administrateur" `
             -Restart
```

### Étape 4.4 : Vérification sur le contrôleur de domaine

1. Ouvrir **Active Directory Users and Computers**
2. Vérifier dans l'OU `Computers` ou l'OU dédiée
3. Le PC doit apparaître avec son nom

**Vérification DNS :**

```powershell
# Sur le DC
Get-ADComputer -Filter {Name -like "NOM_DU_PC"}
```

---

## 🎯 Récapitulatif des commandes utiles

| Action | Commande |
|---|---|
| Forcer mise à jour GPO | `gpupdate /force` |
| Vérifier résultat GPO | `gpresult /r` ou `gpresult /h rapport.html` |
| Liste des ordinateurs AD | `Get-ADComputer -Filter *` |
| Liste des utilisateurs | `Get-ADUser -Filter *` |
| Redémarrer service DHCP | `Restart-Service DHCPServer` |
| Vérifier bail DHCP | `ipconfig /all` (sur client) |

---

## ⚠️ Bonnes pratiques

- 🔒 **Sauvegardes** : Sauvegarder régulièrement l'état système du DC
- 🧪 **GPOs** : Toujours tester sur une OU de test avant déploiement général
- 📌 **DHCP** : Configurer des réservations pour les équipements critiques
- 🛡️ **Sécurité** : Ne jamais utiliser le compte `Administrator` quotidiennement
- 📝 **Documentation** : Documenter toute modification de structure AD
