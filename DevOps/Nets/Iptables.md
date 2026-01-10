Iptables - интерфейс для взаимодействия с netfilter.

![[Pasted image 20260104162600.png|900]]

![[Pasted image 20260104163840.png|900]]

Просмотр iptables для filter:

`sudo iptables -t filter -L -nv
-L - вывести списком
-t filter - вывести таблицы для filter, ещё можно для nat, например
-n - вывод портов и адресов числами
-v - verbose, подробный вывод


Разрешить подключение по ssh (не дропать пакеты):

`sudo iptables -t filter -A INPUT -p tcp --dport=22 -i eth0 -j ACCEPT`
-A - append, добавить в конец правило
-p - протокол
-dport - dest port
-i - интерфейс
-j - действие


Разрешить входящий трафик для уже установленных соединений:

`sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT`
-A INPUT — добавить правило для **входящего** трафика
-m state --state ESTABLISHED,RELATED — для соединений, которые:
  - **ESTABLISHED** — уже активны (ответы на ваши запросы)
  - **RELATED** — связаны с существующими (например, FTP data channel)
-j ACCEPT — **разрешить** этот трафик


Дропать все пакеты, которые не разрешены:

`sudo iptables -P INPUT DROP`
-P - политика

Дропать пакеты с определенного IP:

`sudo iptables -I INPUT -s 42.42.42.42 -j DROP`
-s - source-ip


Дроп пакета - отправитель об этом не узнает(безопастно, но при дебаге неудобно)
Реджект пакета - может быть много реджектов, из-за этого много придется отослать(icmp). Отправить будет уведомлен об этом.


Сохранить конфигурацию iptables:

`sudo iptables-save > ./iptables-rules`

Накатить конфигурацию iptables:

`sudo iptables-restore < ./iptables-rules`