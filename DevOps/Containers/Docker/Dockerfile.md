Пример Dockerfile для NodeJS приложения:

```Dockerfile
FROM registry.yandex.net/rtc-base/jammy:stable AS base

ENV NODE_VERSION=22.8.0
ENV NVM_DIR=/root/.nvm
ENV NVM_NODEJS_ORG_MIRROR="https://mirror.yandex.ru/mirrors/nodejs.org/dist"
ENV PATH="$NVM_DIR/versions/node/v${NODE_VERSION}/bin/:${PATH}"
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

В данном примере используется кэширование через multi-stage builds.
	В итоговый образ попадает всё после последнего FROM base