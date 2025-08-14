Контейнеры — это изолированные процессы в Linux.
Chroot — древний метод изоляции в Unix. Он позволяет назначать корневой каталог процессу. Таким образом, будет изоляция по файловой системе, а по процесса и ресурсам не будет.

Пример использования chroot для создания прото-контейнера:

``` sh
root@darnelo-vm:~# cd /tmp
root@darnelo-vm:/tmp# chroot darnelius/ sh
chroot: failed to run command ‘sh’: No such file or directory
root@darnelo-vm:/tmp# ldd $(which sh)
	linux-vdso.so.1 (0x00007ffe626e6000)
	libc.so.6 => /usr/lib/x86_64-linux-gnu/libc.so.6 (0x00007f1fbf6a9000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f1fbf8fe000)
root@darnelo-vm:/tmp# tar ch /usr/lib/x86_64-linux-gnu/libc.so.6 /lib64/ld-linux-x86-64.so.2 | tar x -C darnelius/
root@darnelo-vm:/tmp# tree darnelius/
darnelius/
├── lib64
│   └── ld-linux-x86-64.so.2
└── usr
    ├── bin
    │   └── sh
    └── lib
        └── x86_64-linux-gnu
            └── libc.so.6

5 directories, 3 files
root@darnelo-vm:/tmp# chroot darnelius/ sh
# pwd
/
# cd ..
# ls
sh: 3: ls: not found
# exit
root@darnelo-vm:/tmp# mkdir darnelius/bin
... scp busybox
root@darnelo-vm:/tmp# tree darnelius/
darnelius/
├── bin
│   └── busybox
├── lib64
│   └── ld-linux-x86-64.so.2
└── usr
    ├── bin
    │   └── sh
    └── lib
        └── x86_64-linux-gnu
            └── libc.so.6

6 directories, 4 files
root@darnelo-vm:/tmp# chroot darnelius/ busybox sh
chroot: failed to run command ‘busybox’: Permission denied
root@darnelo-vm:/tmp# chmod +x darnelius/bin/busybox
root@darnelo-vm:/tmp# chroot darnelius/ busybox sh
/ # ls
sh: ls: not found
/ # /bin/busybox ls
bin    lib64  usr
/ # /bin/busybox --install -s
...
/ # ls -la
total 20
drwxrwxr-x    5 0        0             4096 Aug 11 19:11 .
drwxrwxr-x    5 0        0             4096 Aug 11 19:11 ..
drwxrwxr-x    2 0        0             4096 Aug 11 19:11 bin
drwxrwxr-x    2 0        0             4096 Aug 11 18:58 lib64
lrwxrwxrwx    1 0        0               12 Aug 11 19:11 linuxrc -> /bin/busybox
drwxr-xr-x    4 0        0             4096 Aug 11 18:58 usr
/ # ps
PID   USER     TIME  COMMAND
ps: can't open '/proc': No such file or directory
/ # mkdir /proc
/ # mount -t proc none /proc
/ # ps
PID   USER     TIME  COMMAND
    1 0         7:11 {systemd} /sbin/init
    2 0         0:02 [kthreadd]
    3 0         0:00 [rcu_gp]
    ...
1284909 0         0:00 ps
2351234 0         0:00 /lib/systemd/systemd --user
/ #
```


Для полной изоляции используются пространства имен namespace.

06:50 Пространства имен namespace

• Namespace изолируют процессы по маунтам, сети и PID.
• Процессы наследуют пространство своих родителей.
• Утилита chroot позволяет создавать изолированные контейнеры.

09:05 Пример с chroot и namespace

• Автор создает изолированный контейнер с помощью chroot и namespace.
• Проверяет работу контейнера и его изоляцию.

10:05 Контрольные группы control groups

• Контрольные группы позволяют управлять ресурсами между процессами.
• Пример создания и ограничения ресурсов для контейнера.
• Добавление процесса контейнера в контрольную группу.

12:46 Запуск процесса и проверка контрольных групп

• Запуск процесса с бесконечным циклом, который потребляет память.
• Проверка работы контрольных групп, которые ограничивают ресурсы процесса.
• Логи хостовой машины показывают, что процесс был убит из-за превышения лимита памяти.

14:09 Введение в контейнеры и их создание

• Объяснение важности понимания контейнеров для инженеров.
• Контейнеры как изолированное окружение для выполнения программ.
• Контейнеризация как вид виртуализации и создание систем управления контейнерами.

15:28 Создание контейнера с помощью runc

• Создание директории и выполнение команды для создания спецификации контейнера.
• Объяснение параметров конфигурации контейнера, таких как версия, процессы, пользователь и файловая система.
• Объяснение структуры Docker-образов и их преобразования в контейнеры.

17:25 Запуск контейнера и проверка его работы

• Запуск контейнера с помощью runc и проверка его параметров.
• Проверка файловой системы и стандартных программ внутри контейнера.
• Подведение итогов: запуск полноценного контейнера с помощью runc.

19:35 Заключение и рекомендации

• Важность понимания работы контейнеров и их компонентов.
• Рекомендации по дальнейшему изучению Docker и контейнеров.
• Призыв к подписке и взаимодействию с контентом.




---
Ссылки по материалам видео:
- Различия между Docker, containerd, CRI-O и runc [https://habr.com/ru/companies/domclick/articles/566224/](https://www.youtube.com/redirect?event=comments&redir_token=QUFFLUhqbFdoR2VQYWxaamgxMmV6NnZjMXlzTGUtVjd0QXxBQ3Jtc0ttRFdWTTlPMWl1YnNULVA2S1pPUzNaWS1HQndhdlhTSDFaWmRVUDBJZzI5ZDhra3A2TkMyTkhjQmhrLWN5NGhkNExfNF9hWWt5V1l6YlVpS2FyX0pfWlR1eTJEbDZnVU82ZWt6WEgwanpWNGtkcmtocw&q=https%3A%2F%2Fhabr.com%2Fru%2Fcompanies%2Fdomclick%2Farticles%2F566224%2F) 
- Контейнерная виртуализация в Linux [https://www.youtube.com/watch?v=rJRLZfk3a8U&t=1s&ab_channel=ComputerScienceCenter](https://www.youtube.com/watch?v=rJRLZfk3a8U&t=1s&pp=0gcJCTAAlc8ueATH) 
- Механизмы контейнеризации: namespaces [https://habr.com/ru/companies/selectel/articles/279281/](https://www.youtube.com/redirect?event=comments&redir_token=QUFFLUhqblJpZDN3QzBJTlhoQ2dhQ2U5anlxM1hyZ1F6Z3xBQ3Jtc0trdDFoeEw2dUxJa0NIWFNYd2dLZElhRWFJUXUxWVlFR1RmcERqTG54dFBPQzA3M3dZb1RtLTJ4eTJ5TEJDbG80R08yYk5yam1mU2JlNnE0dVdxaS1ITDRRd3RIS19UT2Rjcl9VSXlFZUV5SzRVWDJXdw&q=https%3A%2F%2Fhabr.com%2Fru%2Fcompanies%2Fselectel%2Farticles%2F279281%2F) 
- Механизмы контейнеризации: cgroups [https://habr.com/ru/companies/selectel/articles/303190/](https://www.youtube.com/redirect?event=comments&redir_token=QUFFLUhqbGpCdFhoMFFFTnhHVDBxVHJjQkFJRzI0akhFUXxBQ3Jtc0tsRlVzTWZNOVhsbUdmOWdRVUFQU1F3NXJZdy1BbmRXRVd4Y09nS005bGd5dXQtZmtrYUI2TzVQbzZwY21QaWxFaC1oUng0TUQzQjhaUUJFTks4OGJVWS1nT3JmbmVKLXpYQ3BES0Z5ZzdvTlkwRFBkYw&q=https%3A%2F%2Fhabr.com%2Fru%2Fcompanies%2Fselectel%2Farticles%2F303190%2F) 
- Кетов. Внутреннее устройство Linux. Глава 9 Capabilities [https://habr.com/ru/companies/otus/articles/471802/](https://www.youtube.com/redirect?event=comments&redir_token=QUFFLUhqbFhNRUVCSzZJNjVnY3NXNkhveGZ5cFF5UER4d3xBQ3Jtc0tuYVY3cmp6cDJiVmhFSHFaN2hCN3JNN1Q2VGNfS19jVzRtSXhlc2JCM2h0Wl85MndJY0VHWGExR1VhU1RDU3VfeEFFQ2VkcTNFalBSdXJfUm9McXJ5Q1VzY25EWUc1aFZjRlpOblNiSWZkQ1FmeTZIMA&q=https%3A%2F%2Fhabr.com%2Fru%2Fcompanies%2Fotus%2Farticles%2F471802%2F) 
- Тут пример с сетью Run your own container without Docker [https://medium.com/@alexander.murylev/run-your-own-container-without-docker-60c297faf010](https://www.youtube.com/redirect?event=comments&redir_token=QUFFLUhqbVJzMFBtLXVVZDZIVXU1SkJQenB3dTNFVmZwUXxBQ3Jtc0ttcFhmM1NEQkdWcXRhOTd2NFBnTkpsUUxrcHhhVklMT3R4Mmt0VVdvb3pyOE4wbnVTdFJua2Q5MW1BYWRPRjlTX3A5ZlpiZXJDS0h2WW9PUkJsXzc2aE9VZmxuUnMtcmNkbGM1dWVzcFUwYi0xOXNDbw&q=https%3A%2F%2Fmedium.com%2F%40alexander.murylev%2Frun-your-own-container-without-docker-60c297faf010) 
- runc & OCI Deep Dive [https://mkdev.me/posts/the-tool-that-really-runs-your-containers-deep-dive-into-runc-and-oci-specifications](https://www.youtube.com/redirect?event=comments&redir_token=QUFFLUhqbWt3SXBoLW9YeUxuakFwdFlJd184Z2RHY09rQXxBQ3Jtc0tuUVB3R05BenFDZ3hCb0FXelFMS3o1Ykx2Z05sYXFCNERrcDJ1Y3k5NlJiaUdyallQWXprSnMxSWlpd1dGVnpxdjFycElZOW9ZZGplWXcyVWdsZlNUYkZ2T0VtOVR1T0xjWDR4RmI1dVpIV2RXaEo2OA&q=https%3A%2F%2Fmkdev.me%2Fposts%2Fthe-tool-that-really-runs-your-containers-deep-dive-into-runc-and-oci-specifications)