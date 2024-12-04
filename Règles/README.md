[⬅️ Sommaire](../README.md)


## Les règles

Les règles dans nftables définissent le traitement des paquets qui traversent les chaînes. elles sont flexbiles et peuvent effectuer des actions telles ques bloquer, autoriser, rediriger, journaliser etc. **Les règles s'inscrivent dans des chaînes, elles-mêmes contenues dans des tables.**   

Une règle se compose de plusieurs parties: 
- **Filtrage**: Conditions pour correspondre au trafic (ex: IP source, protocole, port, etc... )
- **Action**: Ce qui doit être fait avec les paquets correspondants (ex: accept, drop, etc...).

### Structure de base d'un règle
`nft add rule <famille> <table> <chaîne> <conditions> <action>`

| Element | Description |
| --- | --- |
| Famille       | Spécifie la famille de protocoles ( ip, ip6, inet ) |
| Table         | Le nom de la table contenant la chaine |
| Chaine        | La chaine où la règle sera ajoutée |
| Conditions    | Les critères qui déterminent si une règle correspond au paquet |
| Action        | Ce quit doit être fait avec les paquets correspondant |

### Les matchers ( Correspondant )

Les correspondances permettent de définir quelles propriétés du trafic doivent être vérifiées pour appliquer une règle.  

| Matcher | Description | Example |
| --- | --- | --- |
| iif | Interface d'entrée | `iif lo accept` |
| oif | Interface de sortie | `oif lo` |
| ip saddr | Vérifie l'adresse IP source ( ipv4 ) | `ip saddr 192.168.1.1` |
| ip daddr | Vérifie l'adresse IP destination ( ipv4 ) | `ip daddr 10.0.0.0/0` |
| ip6 saddr | Vérifie l'adresse IP source ( ipv6 ) | `ip saddr fe80::1`|
| ip6 daddr | Vérifie l'adresse IP destination ( ipv6 ) | `ip daddr ::1`|
| tcp dport | Vérifie le port TCP de destination | `tcp dport 22` |
| tcp sport | Vérifie le port TCP source | `tcp sport 1024` |
| udp dport | Vérifie le port UDP de destination| `udp dport 53` |
| udp sport | Vérifie le port UDP source | `udp sport 153` |
| ip protocol | Vérifie le protocole IP ( TDP, UDP, ICMP, etc... ) | `ip protocol tcp` |
| ct state | Vérifie l'état de connexion ( new, established, related, etc... ) | `ct state established` |
| ct zone | Vérifie la zone de connexion | `ct zone 1`|
| ct mark | Vérifie le marqueur de connexion | `ct mark 0x10`|
| ct timeout | Vérifie le délai d'expiration des connexions | `ct timeout 300` |
| meta iif | Vérifie l'interface réseau d'entrée | `meta iif lo` |
| meta oif | Vérifie l'interface réseau de sortie | `meta oif eth0`|
| meta mark | Vérifie les marqueurs de paquet | `meta mark 1`|
| meta l4proto | Vérifie le protocole de couche 4 ( transport ) | `meta l4proto tcp`|
| icmp type | Vérifie le type ICMP ( ping, pong etc... ) | `icmp type echo-request`|
| ip ttl | Vérifie la valeur TTL ( time to live ) du paquet | `ip ttl 64`|
| ip tos | Vérifie le champ TOS ( type of service ) | `ip tos 0x10` |
| ip length | Vérifie la longueur totale du paquet IP| `ip length 60`|
| ip frag-offset | Vérifie l'offset de fragmentation | `ip frag-offset 0`|
| ip frag-more| Vérifie si un paquet est fragmenté ( plus de fragments ) | `ip frag-more 1`|
| payload | Analyse une portion spécifique du paquet | `payload offset 10 load 4 eq 0x12345678` |
| icmp6 type | Vérifie le type ICMPv6 ( Neighbor Solicitation, etc ... ) | `icmp6 type 133`|
| tcp flags | Vérifie les drapeaux TCP (SYN, ACK etc... ) | `tcp flags syn ack`|
| tcp option | Vérifie si une option TCP spécifique est définie | `tcp option mss` |
| limit | Limite le nombre de paquets correspondants ( ex: par secondes ) | `limit rate 10/second` |
| hashlimit | Limite les connexions IP source | `hashlimit name ssh_limit burst 5 rate 1/minute`|
| pkttype | Vérifie le type de paquet ( multicast, broadcast etc... ) | `pkttype multicast`|
| bitwise | Compare des bits spécifiques dans un champ de données.| `bitwise mask 0xff shift 0x10` |
| fwd | Vérifie si un paquet est redirigé à une interface spécifique | `fwd to eth1`|
| fib | Vérifie les informations de routage (FIB)| `fib daddr type local`|
| mark | Vérifie les marqueurs de paquets ( souvent pour QoS ) | `mark 0x1`|
| priority | Vérifie la priorité des paquets | `priority 1`|  

### Principales actions 

| Action | Description | Action |
| --- | --- | --- |
| accept | Autorise le paquet à poursuivre sa route | `iif lo accept`|
| drop | Bloque et abandonne le paquet sans réponse | `tcp dport 22 drop`|
| reject | Bloque le paquet et envoie un message de réponse ICMP | `tcp dport 22 reject` |
| log | journalise les informations sur le paquet | `tcp dport 22 log prefix "SSH attempt: " group* 0` |
| counter | Comptabilise les paquets sans affecter leur traitement | `ip saddr 192.168.1.0/24 counter` |
| jump | Transfère le contrôle vers une autre chaine, puis revient. | [Voir un example](./jump.md) |
| goto | Transfère le contrôle vers une autre chaine sans revenir | [Voir un example](./goto.md) |
| dnat | Change la destination d'un paquet (utilisé pour la redirection de port) | [Voir un example](./dnat.md) |
| snat | Change l'adresse source d'un paquet | [Voir un example](./snat.md) |

*L'attribut group 0 définit le gourpe de journalisation ( par défaut, 0)
    
#### Exemples combinés

1. Accepter les paquets TCP port 22 uniquement d'une adresse spécifique  
`nft add rule inet filter input ip saddr 192.168.1.1 tcp dport 22 accept`

2. Bloquer les paquets UDP port 53 (DNS) provenant d'un réseau spécifique  
`nft add rule inet filter input ip saddr 10.0.0.0/8 udp dport 53 drop`   

3. Limiter les connexions SSH ( TCP port 22) à 5 connexions par minute   
`nft add rule inet filter input tcp dport 22 limit rate 5/minute accept` 

4. Accepter uniquement les paquets ICMP de type echo-request (ping)    
`nft add rule inet filter input icmp type echo-request accept`


