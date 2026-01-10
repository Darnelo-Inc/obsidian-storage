В группу `all` входят все хосты, упомянутые в файле inventory.
В группу `ungrouped` входят все хосты без явной группы.

Описывать хосты можно так:
```txt
[staging_servers]
linuxX    ansible_host=172.31.8.69    ansible_user=ec2-user    ansible_ssh_private_key_file=/home/ec2-user/.ssh/california-key1.pem
```

В минимальной конфигурации так:
```txt
[staging_servers]
172.31.8.69
172.31.8.70
```

Опишем несколько групп:

```txt
172.31.8.69
172.31.8.70

[staging_servers]
172.31.8.71
172.31.8.72

[staging_db]
172.31.8.73
172.31.8.74

[staging_web]
172.31.8.75
172.31.8.76
```

Теперь добавим группу, которая будет включать другие группы:

```
...
[staging_all:children]
staging_servers
staging_db
staging_web
```

---

Env-переменные в файле inventory не хранят. Их выносят в каталог `proj_path/group_vars`, где env-переменные кладут в файлы с именами групп хостов, для которых хотим задать переменные.