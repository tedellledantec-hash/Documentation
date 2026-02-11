# Configuration d'une interface virtuelle pour VLAN (Avec DHCP Helper)

# Passer en mode administrateur

  Switch> en

# Se mettre en mode terminal

  Switch#> conf t

# Se mettre sur une interface physique

  Switch (conf t)> interface gigabitEthernet 0/0/1.[NUMÉRO DU VLAN]

  Encapsuler en dot1Q

  Switch (conf t)> encapsulation dot1q [NUMÉRO DU VLAN]

# Activer le DHCP Helper (pour permettre les paquets DHCP)

  Switch (conf t)> ip helper-address [ADRESSE IP DE LA MACHINE]
