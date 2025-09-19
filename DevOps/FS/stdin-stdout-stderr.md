stdin - стандартный вход
stdout - стандартный выход
stderr - стандартный выход ошибок

Разница между stdout и stderr - первый имеет буфер, второй не имеет

\> - перезапись файла
\>> - добавит в конец

---

Команда в стиле Bourne позволяют перенаправлять stderr в то же место назначения, куда направляется stdout с помощью`2>&1` или |&

Пример:

```sh
kit@kit:~$ ls nohup.out hfdksaf | grep 'hup'
ls: cannot accest 'hfdksaf': No such file or directory nohup.out
kit@kit:~$ ls nohup.out hfdksaf 2>&1 | grep 'hup'
nohup.out
```

Запись в файл `>`. Чтобы записать также stderr - `2>&1` или `&>`
