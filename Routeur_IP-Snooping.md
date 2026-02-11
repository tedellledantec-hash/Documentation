# Sécurisation d'un routeur contre l’IP Spoofing

## Passer en mode administrateur
```text
Switch> en
```

## Se mettre en mode terminal
```text
Switch# conf t
```

##  l’usurpation d’adresses IP
```text
Switch(conf t)> interface range gigabitEthernet 1/0/1-28
Switch(if-range)> ip verify source port-security
```

## Affichage

### Afficher les machines bloquées
```text
Switch# show ip verify source
```

### Afficher les machines connectées sur le routeur
```text
Switch# show ip source binding
```
