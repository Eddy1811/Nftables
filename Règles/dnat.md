[⬅️ Retour aux règles](./README.md)
```
table ip nat {
    chain prerouting {
        type nat hook prerouting priority -100; policy accept;
        tcp dport 80 dnat to 192.168.1.10:8080
    }
}
```