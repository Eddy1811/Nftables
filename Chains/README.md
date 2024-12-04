[⬅️ Sommaire](../README.md)



## Chains

Dans nftables, les chaînes sont des conteneurs au sein des tables, qui organisent les règles. Chaque chaîne est associée à un **hook** ( point d'entrée dans le traitement du trafic ) et une **priorité**. Les chaînes permettent de définir des actions sur des paquets à différents moments du cycle de traitement réseau.  

Il existe deux types de chaînes :
- Les chaînes de base: reliées à un hook réseau ( automatiquement exécutées).
- Les chaînes définies par l'utilisateur: Chaînes personnalisées qui peuvent être appelées depuis d'autres chaînes.

| Chaines | Description | 
| --- | --- |
| input | Utilisée pour gérer les paquets entrants |
| output | Gère les paquets sortants émis par le systeme local |
| forward | Gère les paquets transmis via le système ( routage entre les interfaces )|
| prerouting | Capture les paquets avant qu'ils ne soient routés ( Souvent utilisée pour NAT ) |
| postrouting | Capture les paquets juste avant qu'ils ne quittent le système ( Souvent utilisée pour NAT ) |

```lua 
table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;
    }

    chain output {
        type filter hook output priority 0; policy accept;
    }
}
```

### Ajouter une chain à une table 

```lua
nft add chain <type_de_table> <nom_de_la_table> <my_chain>
# ex : nft add chain ip libvirt_network my_chain
```