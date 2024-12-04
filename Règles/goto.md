[⬅️ Retour aux règles](./README.md)

```
table ip filter {
    chain input {
        type filter hook input priority 0; policy accept;
        ip saddr 10.0.0.0/8 goto custom_chain
    }

    chain custom_chain {
        tcp dport 22 accept
        drop
    }
}
```