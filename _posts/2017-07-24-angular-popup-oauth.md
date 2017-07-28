---
title: Героический отлов OAuth callback в Angular
layout: post
date: '2017-07-24 21:47:11 +0300'
category: Frontend
author: "Artamoshkin Maxim"
tags:
- Angular
- OAuth
- SPA
- JavaScript
- TypeScript
description: "Сегодня расскажу о небольшом лайфхаке в плане OAuth авторизации в SPA, а конкретно в Angular приложении. Многие, наверняка, сталкивались с авторизацией через социальные сети, и все ясно представляют этот процесс, делаем GET запрос и делаем редирект к OAuth серверу с нужными параметрами (id приложения, скопами, и главное с URL редиректа) => в случае не авторизованного пользователя сервер отвечает html’ем с вводом креденшалов пользователя => после успешной авторизации OAuth сервер переходит на наше приложение, т.е. на заданный нами URL =>  и все, далее пользователь погнал юзать наше, невиданной полезности, приложение. Как бы тут все просто. Но… Бывают случаи, когда очень нежелательно уходить с приложения:" 
---

Сегодня расскажу о небольшом лайфхаке в плане OAuth авторизации в SPA, а конкретно в Angular приложении. Многие, наверняка, сталкивались с авторизацией через социальные сети, и все ясно представляют этот процесс, делаем GET запрос и делаем редирект к OAuth серверу с нужными параметрами (id приложения, скопами, и главное с URL редиректа) => в случае не авторизованного пользователя сервер отвечает html’ем с вводом креденшалов пользователя => после успешной авторизации OAuth сервер переходит на наше приложение, т.е. на заданный нами URL =>  и все, далее пользователь погнал юзать наше, невиданной полезности, приложение. Как бы тут все просто. Но… 

<!-- more -->

Бывают случаи, когда очень нежелательно уходить с приложения:



<br>

- Пользователю станет лениво, он не будет вводить логин\пароль и просто уйдет, закрыв вкладку
- Нажмет назад, а его вернет, допустим, на начальный шаг какой-то процедуры, т.к. это может находиться на одном пути (route), SPA же.
- Обновление токена в один клик, без перезагрузки приложения
И тд…

Очевидно, что без popup тут не обойтись. Но вопрос, куда же направлять редирект. «Может быть прописать отдельный путь для редиректа, добавить компонент, который обработает колбек и синхронизирует с основным.» WAT? Нет, это  драгоценные секунды загрузки и уж больно костыльно. Но не надо отчаиваться, сейчас расскажу свой менее болезненный и уже проверенный временем способ.
Нужно всего лишь создать html корне приложения, например, oauth-callback.html. И делать редирект именно на него
`https://{my-domain}/oauth-callback.html`

Вы можете сказать, мол «как бы ok, и че, а как в родительском окне токен получить?». Здесь тоже все просто, из родительского окна мы имеем доступ к объекту window дочернего окна.
«То есть мы можем сделать setInterval и проверять какие данные, пользователь вводит данные или уже ввел, и мы имеем callback, а потом по window.location вытащить токен?» 
Свят, свят. Нет!  Добавим в html следующие строки: 

```ts  
<!DOCTYPE html>
<html>
<head>  
    <script>
        var oAuthCallbackEvent = new CustomEvent("OAuthCallback");
        window.opener.dispatchEvent(oAuthCallbackEvent);
        window.close();
    </script>
</head>
<body>
</body>
</html>
```

Создадим кастомный эвент, который будем слушать в родительском окне, и который будет означать успешную авторизацию в социалке и то, что был возвращен callback.

Кстати, создается popup, примерно, следующим способом: 

```ts
let width = 860;
let height = 500;
let left = (screen.width / 2) - (width / 2);
let top = (screen.height / 2) - (height / 2);
let windowOptions = `menubar=no,location=no,resizable=no,scrollbars=no,status=no, width=${width}, height=${height}, top=${top}, left=${left}`;
let type = 'auth';

window.open(url, type, windowOptions);
```

А в компонент, где создается окно авторизации, добавим слушатель через Renderer2:

```ts
this._renderer.listen('window', 'OAuthCallback',
    (evt) => {
        this._globalOAuthCallbackListenFn();

		// тут сохраняем наш токен или производим другие действия после успешной авторизации
    });
```

Добавим в эвент деталей, для передачи токена.
```ts 
var oAuthCallbackEvent = new CustomEvent("OAuthCallback", {'detail': window.location.search});
```

Теперь мы можем получить данные через эвент: `evt.detail`.

В результате мы получаем абсолютно спокойное приложение, которое без нервных движений реагирует на действия пользователя. 