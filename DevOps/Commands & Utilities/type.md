Альтернатива which для bash

```sh
root@darnelo-vm:~# type -a ls
ls is aliased to `ls --color=auto'
ls is /usr/bin/ls
ls is /bin/ls
```

Сначала ls развернется в alias(если задан), затем поищется бинарник в builtin функциях оболочки, потом в пользовательских функциях, а затем в остальных заданных путях