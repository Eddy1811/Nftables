[⬅️ Sommaire](../README.md)

## Tables 

Dans nftables, une table est une structure qui regroupe des chaînes et des règles. Les tables peuvent être utilisées pour différents types de trafic réseau. Voici les types de tables disponibles, basées sur les familles de protocoles.   

| Famille | Description | Cas d'usage |
| --- | --- | --- |
| ip | Pour le trafic ipv4 uniquement| Trafic interne ou local en IPV4 |
| ip6 | Pour le trafic ipv6 uniquement | Trafic interne ou local en IPV4 |
| inet | Unification des règles ipv4 et ipv6 | Simplification de la gestion |
| arp | Gère le protocole ARP | Protection contre le spoofing ARP |
| bridge | Gestion du trafic sur les ponts réseau | Controle les communication pontées. |
| netdev | Règles attachées aux périphériques réseau | Filtrage très bas niveau |
        
### Gestion des tables en CLI    

**Ajouter une table IPV4**
```
nft add table ip my_table
```

**Ajouter une table IPV4 et IPV6** 
```
nft add table inet my-table
```

### Gestion des tables dans un fichier de configuration

```lua
# Le nom firewall peut être modifié !!!

table inet firewall {
    mes chaines ...
}
```