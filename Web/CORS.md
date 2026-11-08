## CORS: «почему браузер ругается»

Вернёмся к сайту бабушки. Она положила `index.html`, картинки, всё чудесно работает в браузере.

А потом она решила сделать на сайте JS-виджет: «загрузите свою фотку хлеба, мы покажем вам похожие». Загрузка идёт через `fetch('https://photos-bucket.s3-is-awesome.net/...')` прямо из браузера.

И браузер в консоли пишет:

```
Access to fetch at 'https://photos-bucket...' from origin 'https://babushka-pekarnya...'
has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present
on the requested resource.
```

Что происходит?

### Same-origin policy и зачем CORS

В браузерах есть фундаментальное правило безопасности: **same-origin policy**. JavaScript, загруженный с `https://babushka-pekarnya.com/`, **не может** по умолчанию делать запросы на `https://photos-bucket.s3-is-awesome.net/`. Потому что если бы мог — любой сайт в интернете мог бы через ваш браузер залезть в ваш банк и списать деньги (сейчас, конечно же, нет, но должно быть страшно).

Origin — это тройка `(схема, хост, порт)`. Если хоть что-то отличается — это разные origins. `https://a.com` и `https://b.com` — разные. `https://a.com` и `http://a.com` — тоже разные.

Запросы между разными origin — **cross-origin**. Они тоже бывают нужны: CDN, API, сторонние сервисы, да тот же S3. Поэтому придумали **CORS** (Cross-Origin Resource Sharing) — механизм, через который сервер может **явно разрешить** другим origins дёргать себя.

### Как работает CORS

Браузер перед «небезопасным» cross-origin запросом (любой `PUT`, `DELETE`, кастомные заголовки, `Content-Type` кроме простых) делает **preflight-запрос**:

```http
OPTIONS https://photos-bucket.s3-is-awesome.net/upload-me.jpg
Origin: https://babushka-pekarnya.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: x-amz-content-sha256, authorization
```

«Эй, сервер, можно я с этого origin сделаю PUT с такими-то заголовками?»

Сервер отвечает:

```http
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://babushka-pekarnya.com
Access-Control-Allow-Methods: PUT, GET
Access-Control-Allow-Headers: x-amz-content-sha256, authorization
Access-Control-Max-Age: 3600
```

«Да, можно. Все эти методы и заголовки разрешены. И не спрашивай меня об этом ещё час.»

Только после этого браузер делает сам полезный запрос. Если на preflight сервер ответил «нет» или не ответил вовсе — запрос до S3 даже не дойдёт.