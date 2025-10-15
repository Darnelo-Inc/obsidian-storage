Заголовок IPv6-пакета:

![[Pasted image 20251014190628.png|1200]]

Описание полей:

- _Version_: версия протокола; для IPv6 это значение равно 6 (значение в битах — 0110).
- _Traffic Class_: приоритет пакета (8 бит). Это поле состоит из двух значений. Старшие 6 бит используются [DSCP](https://ru.wikipedia.org/wiki/DSCP "DSCP") для классификации пакетов. Оставшиеся два бита используются [ECN](https://ru.wikipedia.org/wiki/Explicit_Congestion_Notification "Explicit Congestion Notification") для контроля перегрузки
- _Flow Label_: [метка потока](https://ru.wikipedia.org/wiki/IPv6#%D0%9C%D0%B5%D1%82%D0%BA%D0%B8_%D0%BF%D0%BE%D1%82%D0%BE%D0%BA%D0%BE%D0%B2 "IPv6").
- _Payload Length_: (16 бит) размер данных в октетах, не включая данный заголовок, но включая все расширенные заголовки.
- _Next Header_: задаёт тип расширенного заголовка ([англ.](https://ru.wikipedia.org/wiki/%D0%90%D0%BD%D0%B3%D0%BB%D0%B8%D0%B9%D1%81%D0%BA%D0%B8%D0%B9_%D1%8F%D0%B7%D1%8B%D0%BA "Английский язык") IPv6 extension), который идёт следующим. В последнем расширенном заголовке поле _Next Header_ задаёт тип транспортного протокола ([TCP](https://ru.wikipedia.org/wiki/TCP "TCP"), [UDP](https://ru.wikipedia.org/wiki/UDP "UDP") и т. д.)
- _Hop Limit_: аналог поля time to live в IPv4 (8 бит).
- _Source Address_ и _Destination Address_: адрес отправителя и получателя соответственно; по 128 бит.

С целью повышения производительности и с расчётом на то, что современные технологии канального и транспортного уровней обеспечивают достаточный уровень обнаружения ошибок, заголовок не имеет контрольной суммы.