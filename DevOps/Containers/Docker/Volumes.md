`docker run -v ${PWD}:/usr/share/nginx/html nginx` - маппинг вольюмов на хосте и контейнере через маунт src в dst

Можно завести вольюмы в докер-хосте. Например, указав в файле docker-compose следующее:

```yaml
services:
  ...
  mysql:
	image: mysql
	...
	volumes:
	  - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```


Команда ` docker volume ls` выведет все тома на docker-хосте.

При разработке, чтобы файлы обновлялись в реалтайме, стоит подключить файлы в качестве вольюмов. Файлы, например node_modules, можно оставить, прописав маппинг без двоеточия и только путь к node_modules в контейнере.
Пример:

```yaml
services:
. frontend:
	build: ./frontend
	volumes:
	  - /app/node_modules
	  - ./frontend:/app
	restart: always
	ports:
	  - "3000:3000"
``