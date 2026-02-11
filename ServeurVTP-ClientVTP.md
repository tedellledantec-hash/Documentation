# Mise en place du VTP

## Configuration du Switch VTP Server

### Création des VLANs

```cisco
Switch(config)# vlan X (ex : vlan 10)
Switch(config-vlan)# name VLAN_NAME (ex : Vlan Admin)
Switch(config-vlan)# exit
```

### Assignation des VLANs aux interfaces

Assignez les ports aux VLANs appropriés :

```cisco
Switch(config)# interface gigabitEthernet 0/X
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk allowed vlan X,Y,Z    (10,20,30)
Switch(config-if)# switchport nonegotiate
Switch(config-if)# switchport trunk native vlan X   (vlan 1)
Switch(config-if)# exit
```

### Configuration du serveur VTP

Configurez le switch comme serveur VTP :

```cisco
Switch(config)# vtp mode server
Switch(config)# vtp domain DOMAIN_NAME      (ex : bts-sio.local)
Switch(config)# vtp password XXXXXXXXXX     (ex : Btssio2017)
Switch(config)# vtp version 2
Switch(config)# end
```

## Configuration du Switch VTP Client


### Configuration du client VTP

[!Conseil:)]
 Le trunk entre le switch VTP Server et le switch VTP Client doit être configuré avant de configurer le VTP Client.



```cisco
Switch(config)# vtp mode client
Switch(config)# vtp domain XXXXXXXXXXXX
Switch(config)# vtp password XXXXXXXXXX
Switch(config)# vtp version 2
Switch(config)# end
```
