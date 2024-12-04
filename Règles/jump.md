[⬅️ Retour aux règles](./README.md)

```
table ip filter {
    chain input {
        type filter hook input priority 0; policy accept;
        ip saddr 192.168.1.0/24 jump custom_chain
    }

    chain custom_chain {
        tcp dport 80 accept
        tcp dport 443 accept
    }
}
```