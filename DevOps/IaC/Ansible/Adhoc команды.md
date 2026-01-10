Adhoc-команды – это все команды вне плейбуков.

Условно есть `ansible.cfg` с путём до файла inventory по умолчанию и файл `inventory`, содержащий следующее:
```txt
[staging_servers]
linuxX    ansible_host=172.31.8.69    ansible_user=ec2-user    ansible_ssh_private_key_file=/home/ec2-user/.ssh/california-key1.pem

[prod_servers]
linux1    ansible_host=172.31.27.16    
linux2    ansible_host=172.31.19.188    

[prod_servers:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=/home/ec2-user/.ssh/california-key2.pem
```

`ansible all -m setup` – получение информации о настройке всех серверов

`ansible all -m shell -a "uptime"` – запуск shell-команд на всех хостах и вывод их в stdout текущего терминала
`ansible all -m command -a "uptime"` – как shell, но недоступны env, пайпы, <, > итп.

`ansible all -m copy -a "src=tmp dest=/tmp mode=777"` – копирует файл со всеми правами с мастера(или где выполняется команда) на all сервера
`ansible all -m file -a "path=/tmp/tmp state=absent"` – удаляет файл на серверах

`ansible all -m get_url -a "url=some_url dest=/tmp"` – скачать файл по урлу и положить на хосты по пути dest

`ansible all -m yum -a "name=stress state=install" -b` – установка пакета на все хосты
`ansible all -m yum -a "name=stress state=removed" -b` – удаление пакета на всех хостах

`ansible all -m uri -a "url=some_url return_content=yes" -b` – сходить по урлу и вернуть контент сайта

`ansible all -m yum -a "name=httpd state=latest" -b` – установка апачи

`ansible all -m yum -a "name=httpd state=started enabled=yes" -b` – запустить сервис и переподнимать его при перезагрузке

`ansible-doc -l – вывести список модулей ansible

