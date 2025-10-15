**Порт** – 16-разрядное число, указывающее на канал взаимодействия. Стандартные службы, такие как SMTP, SSH и HTTP, ассоциируются с хорошо известными портами, определёнными в файле `/etc/services`. 

```
...
smtp             25/udp     # Simple Mail Transfer
smtp             25/tcp     # Simple Mail Transfer
...
domain           53/udp     # Domain Name Server
domain           53/tcp     # Domain Name Server
...
http             80/udp     www www-http # World Wide Web HTTP
http             80/tcp     www www-http # World Wide Web HTT
...
kerberos         88/udp     # Kerberos
kerberos         88/tcp     # Kerberos
...
```

Системы UNIX ограничивают программы привязки к номерам портов от 1 по 1023, если они не выполняются от рута.