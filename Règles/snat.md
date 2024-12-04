[⬅️ Retour aux règles](./README.md)

```
table ip nat {
    chain postrouting {
        type nat hook postrouting priority 100; policy accept;
        ip saddr 192.168.1.0/24 snat to 203.0.113.1
    }
}
```