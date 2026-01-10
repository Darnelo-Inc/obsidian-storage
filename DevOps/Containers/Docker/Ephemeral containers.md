## Что это
Временные контейнеры для отладки уже запущенных Pod в Kubernetes.

## Зачем
Отлаживать минимальные образы (distroless, alpine) без shell и утилит.

## Как использовать

Добавить временный контейнер с утилитами
`kubectl debug mypod -it --image=busybox --target=mycontainer`

С Ubuntu
`kubectl debug mypod -it --image=ubuntu --target=mycontainer -- bash`

Для сетевой отладки
`kubectl debug mypod -it --image=nicolaka/netshoot --target=mycontainer`
## Возможности

✅ Видят процессы целевого контейнера (ps aux)  
✅ Доступ к сети целевого контейнера  
✅ Доступ к файлам через /proc/1/root/  
✅ Можно устанавливать любые утилиты

❌ Не перезапускаются  
❌ Нельзя задать ресурсы/порты  
❌ Исчезают после удаления

## Отладка Node
Получить доступ к Node
`kubectl debug node/mynode -it --image=ubuntu`
## Итого
Временный debug-контейнер рядом с production-контейнером, не меняя его образ. 🐛