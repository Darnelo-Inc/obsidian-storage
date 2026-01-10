Dockerfile содержит инструкции для создания образов. Каждая инструкция создаёт слой, который может кэшироваться.

Пример Dockerfile для NodeJS приложения:

```Dockerfile
FROM registry.yandex.net/rtc-base/jammy:stable AS base

ENV NODE_VERSION=22.8.0 \
	NVM_DIR=/root/.nvm \
	NVM_NODEJS_ORG_MIRROR="https://mirror.yandex.ru/mirrors/nodejs.org/dist" \
	PATH="$NVM_DIR/versions/node/v${NODE_VERSION}/bin/:${PATH}"
RUN apt-get update && apt-get install -y curl build-essential \
	&& curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash \
	&& . "$NVM_DIR/nvm.sh" \
	&& nvm install ${NODE_VERSION} \
	&& nvm use v${NODE_VERSION} \
	&& nvm alias default v${NODE_VERSION}

FROM base AS install

WORKDIR /usr/src/app
COPY package.json package-lock.json .npmrc .
RUN npm ci

FROM install AS build

COPY . .
ENV NODE_ENV=production
RUN npm run build

FROM base

WORKDIR /usr/src/app
COPY --from=install /usr/src/app/node_modules/ ./node_modules/
COPY --from=install /usr/src/app/package.json .
COPY --from=build /usr/src/app/dist/ ./dist/

CMD ["npm", "start"]
```

В данном примере используется кэширование через multi-stage builds. В итоговый образ попадает всё после последнего FROM base.

Запустить сборку образа можно с помощью команды
`docker build . -t my-calendar:4.1.3` - точкой указано, где искать Dockerfile. Если он называется по-другому, то через опцию -f нужно указать его имя. Опция -t позволяет задать имя и тег для собираемого образа. Не написан тег – Docker укажет его как latest.

| Инструкция | Описание                                                                                                                                                                                                   |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| FROM       | Задаёт базовый образ.                                                                                                                                                                                      |
| LABEL      | Описывает метаданные. Например — сведения о том, кто создал и поддерживает образ.                                                                                                                          |
| ENV        | Устанавливает постоянные переменные среды.                                                                                                                                                                 |
| RUN        | Выполняет команду и создаёт слой образа. Используется для установки в контейнер пакетов.                                                                                                                   |
| COPY       | Копирует в контейнер файлы и директории.                                                                                                                                                                   |
| ADD        | Копирует файлы и директории в контейнер, может распаковывать локальные .tar-файлы.                                                                                                                         |
| CMD        | Описывает команду с аргументами, которую нужно выполнить когда контейнер будет запущен. Аргументы могут быть переопределены при запуске контейнера. В файле может присутствовать лишь одна инструкция CMD. |
| WORKDIR    | Задаёт рабочую директорию для следующей инструкции.                                                                                                                                                        |
| ARG        | Задаёт переменные для передачи Docker во время сборки образа.                                                                                                                                              |
| ENTRYPOINT | Предоставляет команду с аргументами для вызова во время выполнения контейнера. Аргументы не переопределяются.                                                                                              |
| EXPOSE     | Указывает на необходимость открыть порт.                                                                                                                                                                   |
| VOLUME     | Создаёт точку монтирования для работы с постоянным хранилищем.                                                                                                                                             |