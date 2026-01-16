# Documentation
# Sécurisation d’un Switch Cisco contre les attaques DHCP
*Contexte : Kali Linux, Debian, Switch Cisco*

---

## 1. Contexte et Environnement

### 1.1. Kali Linux
**Kali Linux** est une distribution Linux spécialisée dans la sécurité informatique et les tests d’intrusion. Elle est souvent utilisée pour simuler des attaques réseau, comme le **DHCP Starvation** ou le **MAC Spoofing**.

### 1.2. Debian (isc-dhcp-server)
**Debian** est une distribution Linux stable, souvent utilisée pour héberger des services réseau comme un serveur DHCP (`isc-dhcp-server`). Les commandes `journalctl` et `systemctl` permettent de surveiller et gérer ce service.

### 1.3. Switch Cisco
Les **switches Cisco** sont des équipements réseau permettant de segmenter et sécuriser un réseau local. Les fonctionnalités comme le **DHCP Snooping** et la **Port Security** sont essentielles pour se protéger contre les attaques réseau.

---

## 2. Défense contre le DHCP Spoofing

### 2.1. Activation du DHCP Snooping
Le **DHCP Snooping** permet de filtrer les messages DHCP non autorisés en identifiant les ports "de confiance" (trusted) et en limitant le nombre de requêtes DHCP par port.

```bash
conf t
ip dhcp snooping
ip dhcp snooping vlan 1
interface gig0/1
    ip dhcp snooping trust
ip dhcp snooping information option
interface range fa0/1-24
    ip dhcp snooping limit rate 20

### 2. Simulation d’Attaques depuis Kali Linux
DHCP Starvation
But : Épuiser le pool d’adresses IP du serveur DHCP.

yersinia dhcp -attack 1 -interface eth0
tcpdump -i eth0 port 67 or port 68

MAC Spoofing
But : Usurper une adresse MAC pour contourner les contrôles d’accès.
Surveillance côté Debian :

journalctl -u isc-dhcp-server
systemctl status isc-dhcp-server

### 3. Sécurisation Avancée du Switch
Configuration des Ports
But : Limiter les adresses MAC et activer la sécurité portuaire.

enable
configure terminal
ip dhcp snooping
ip dhcp snooping vlan 1
ip dhcp snooping information option

interface gig0/1
 ip dhcp snooping trust
 exit

interface range fa0/1-24,gig0/2
 switchport mode access
 switchport access vlan 1
 switchport port-security
 switchport port-security maximum 4
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
 ip dhcp snooping limit rate 4

Réactivation d’un Port Désactivé :

int fa0/1
 shutdown
 no shutdown

###  4. Vérification des Adresses MAC
show mac address-table count
show mac address-table

show mac address-table : Liste toutes les adresses MAC apprises par le switch
