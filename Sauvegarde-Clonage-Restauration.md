# Guide — Sauvegarde & Restauration Système

---

# PARTIE 1 — Sauvegarde (Serveur Proxmox)

> Serveur : `172.17.0.13` | Conteneur LXC : `102` | Stockage : `/var/lib/vz/SI01-CG/dump/`

---

## 1.1 Backup Proxmox (vzdump)

Sauvegarde complète du conteneur LXC 102 au format `.tar.zst`.

### Commande de sauvegarde

```bash
vzdump 102 --dumpdir /var/lib/vz/SI01-CG/dump --compress zstd --mode snapshot
```

Fichier généré :
```
/var/lib/vz/SI01-CG/dump/vzdump-lxc-102-2026_03_20-09_12_29.tar.zst
```

### Transfert du backup vers Windows (SCP)

```bash
scp root@172.17.0.13:/var/lib/vz/SI01-CG/dump/vzdump-lxc-102-2026_03_20-09_12_29.tar.zst "C:\Users\tedel\Downloads"
```

---

## 1.2 Snapshot Proxmox

Capture l'état du conteneur à un instant T, sans interruption de service.

### Créer un snapshot

```bash
pct snapshot 102 snap-20260320 --description "Avant mise à jour"
```

### Lister les snapshots

```bash
pct listsnapshot 102
```

### Supprimer un snapshot

```bash
pct delsnapshot 102 snap-20260320
```

> ⚠️ Un snapshot est stocké localement — il ne remplace pas un backup externe.

---

## 1.3 Clone avec Clonezilla

Clonezilla permet de cloner intégralement un disque ou une partition vers un autre support.

### Procédure

1. Démarrer la machine depuis le **live USB Clonezilla**
2. Choisir `device-to-device` (disque vers disque) ou `device-to-image` (disque vers fichier image)
3. Sélectionner le **disque source** puis la **destination** (disque ou répertoire réseau/USB)
4. Lancer le clonage — Clonezilla copie secteur par secteur

### Modes principaux

| Mode               | Usage                                      |
|--------------------|--------------------------------------------|
| `device-to-device` | Cloner un disque vers un autre disque      |
| `device-to-image`  | Créer une image du disque (fichier `.img`) |
| `image-to-device`  | Restaurer une image sur un disque         |

---

## 1.4 Sauvegarde des données (Proxmox)

Sauvegarde ciblée de données spécifiques depuis le conteneur LXC.

### Copier un dossier depuis le conteneur vers l'hôte

```bash
# Depuis l'hôte Proxmox
pct pull 102 /chemin/dans/le/ct /var/lib/vz/SI01-CG/dump/data-backup-102.tar.gz
```

### Ou via tar + scp

```bash
# Dans le conteneur LXC (pct enter 102)
tar czf /tmp/data-backup.tar.gz /var/www /etc

# Depuis l'hôte, récupérer l'archive
scp root@172.17.0.13:/tmp/data-backup.tar.gz "C:\Users\tedel\Downloads"
```

---

# PARTIE 2 — Restauration

---

## 2.1 Restauration Proxmox (LXC)

Restaurer le conteneur 102 depuis l'archive de backup.

### Via CLI

```bash
pct restore 102 /var/lib/vz/SI01-CG/dump/vzdump-lxc-102-2026_03_20-09_12_29.tar.zst \
  --storage local-lvm --force
```

### Via l'interface web Proxmox

1. Aller dans *Node → Stockage local → CT Backups*
2. Sélectionner `vzdump-lxc-102-2026_03_20-09_12_29.tar.zst`
3. Cliquer sur **Restore** → choisir l'ID et le stockage cible

---

## 2.2 Restauration système Windows

Restauration du système Windows à partir d'un point de restauration ou d'une image système.

### Depuis Windows (interface graphique)

1. Ouvrir `Panneau de configuration → Système → Protection du système`
2. Cliquer sur **Restauration du système**
3. Choisir un point de restauration dans la liste
4. Suivre l'assistant et redémarrer

### Depuis l'environnement de récupération (WinRE)

1. Démarrer sur un **support Windows (USB/DVD)**
2. Choisir `Réparer l'ordinateur → Dépannage → Options avancées`
3. Sélectionner **Restauration du système** ou **Récupération de l'image système**
4. Pointer vers l'image `.wim` ou le dossier de sauvegarde

### Restaurer depuis une image Clonezilla

1. Démarrer sur le **live USB Clonezilla**
2. Choisir `image-to-device`
3. Sélectionner l'image source (USB, réseau, NAS)
4. Sélectionner le disque de destination
5. Lancer la restauration

---
  by tedell
