# Comprendre et configurer NFTABLES

**Sommaire**
| Parties | lien |
| --- | --- |
| tables | [Lien](./Tables/README.md) |
| chains | [Lien](./Chains/README.md) |
| Les règles | [Lien](./Règles/README.md)|
| Vie d'un paquet | [Lien](./Paquets/README.md)|


## Exemple de configuration

```
flush ruleset                                                                    
                                                                                 
table inet firewall {
                                                                                 
    chain inbound_ipv4 {
        # accepting ping (icmp-echo-request) for diagnostic purposes.
        # However, it also lets probes discover this host is alive.
        # This sample accepts them within a certain rate limit:
        #
        # icmp type echo-request limit rate 5/second accept      
    }

    chain inbound_ipv6 {                                                         
        # accept neighbour discovery otherwise connectivity breaks
        #
        icmpv6 type { nd-neighbor-solicit, nd-router-advert, nd-neighbor-advert } accept
                                                                                 
        # accepting ping (icmpv6-echo-request) for diagnostic purposes.
        # However, it also lets probes discover this host is alive.
        # This sample accepts them within a certain rate limit:
        #
        # icmpv6 type echo-request limit rate 5/second accept
    }

    chain inbound {                                                              

        # By default, drop all traffic unless it meets a filter
        # criteria specified by the rules that follow below.
        type filter hook input priority 0; policy drop;

        # Allow traffic from established and related packets, drop invalid
        ct state vmap { established : accept, related : accept, invalid : drop } 

        # Allow loopback traffic.
        iifname lo accept

        # Jump to chain according to layer 3 protocol using a verdict map
        meta protocol vmap { ip : jump inbound_ipv4, ip6 : jump inbound_ipv6 }

        # Allow SSH on port TCP/22 and allow HTTP(S) TCP/80 and TCP/443
        # for IPv4 and IPv6.
        tcp dport { 22, 80, 443} accept

        # Uncomment to enable logging of denied inbound traffic
        # log prefix "[nftables] Inbound Denied: " counter drop
    }                                                                                                                                                      
    chain forward {                                                              
        # Drop everything (assumes this device is not a router)                  
        type filter hook forward priority 0; policy drop;                        
    }

    chain prerouting {
        type nat hook prerouting priority -100; policy accept;
        tcp dport 80 dnat to 192.168.1.10:8080
    }                                                                            
                                                                                 
    # no need to define output chain, default policy is accept if undefined.
}
```