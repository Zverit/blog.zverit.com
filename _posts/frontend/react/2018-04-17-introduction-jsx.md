---
layout: post
title:  "Разработка на React. JSX"
date:   2018-04-17 22:17:13 +0300
category: Frontend
author: "Artamoshkin Maxim"
image: "/assets/introduction-jsx.png"
redirect_from:
image_alt: ""
image_height: "auto"
redirect_from:
    - /frontend/2018/04/16/introduction-jsx/
tags: [React, ReactJS, Lessons, JavaScript, JS, JSX]
description: "В данной статье рассмотрен синтаксис и особенности JSX, его применение в React приложениях, а также пути его внедрения."
---

Ранее мы создавали React элемент при помощи следующего синтаксиса:

```js
React.createElement('h1', null, 'heading');
```

Это не подходит, если ведется разработка большого приложения со сложным интерфейсом. Используя только этот синтаксиc разработка сильно замедлится, так как он слишком многословен. Поэтому компания Facebook представила синтаксис JSX (*JavaScript Syntax Extension*), который сильно напоминает язык разметки HTML, но по факту это расширение JavaScript.
 
<!-- more -->

### Сравнение с createElement ###

Ранее мы создавали элемент `h1` при помощи `createElement`

```js
ReactDOM.render(
  React.createElement('h1', null, 'First React App'),
  document.getElementById('root')
);
```

То же самое на JSX будет выглядеть следующим образом:
```html
<h1>First React App</h1>
```

Выглядит как обычный HTML код, но не стоит путать. JSX – синтаксический сахар для `createElement`. 

### Использование ###

JSX это расширение JavaScript, по этому все также можем использовать всю силу JS, лишь с некоторыми замечаниями.

#### Выражения ####

Внутри JSX можно использовать выражения или переменные JavaScript, поместив лишь в фигурные скобки.

```html
<h1>{name}</h1>
```

У JSX кода те же возможности как и у JS объектов: присваивание переменным, передача в функции и возврат как результат работы в функций и т.д.

```jsx
const el = (
	<h1> This is object </h1>
);
```

```jsx
<h1> Hi, {user ? user.name : ‘stranger’}</h1>
```

#### className ####

Слово class уже зарегистрировано в JavaScript, поэтому используется альтернативное – `className`. 

```jsx
<button className=”btn btn-small”> Button </button>
```

#### Массивы ####

Так как JSX, включает функциональность JS, то и вывод происходит через перебирающие методы.

```jsx
<div class=”gallery”>
{this.props.images.map((image, i) => 
	<img key={i} src={image.url} />
)}
</div>
```

JSX также как HTML имеет уровни вложенности. Дочерние элементы могут содержать в себе как стандартные HTML теги, так и созданные компоненты, о которых поговорим позже.

#### Комментарии #### 
Так же, как и в JS для комментирования можно использовать  `//` либо для многострочных `/* … */`

### Транспиляция ###

Конечно же, код в JSX не может быть выполнен в обычном браузере и поэтому он подвергается постобработке – транспиляции. 
Транспиляция – это процесс перевода исходного кода одного языка в другой.
Для транспилирования JSX в JavaScript применяется транспайлер [Babel](https://babeljs.io/ "Babel"){:target="_blank"}{:rel="noopener"}. 

Кроме того, мы можем использовать новые спецификации JavaScript не задумываясь о его поддержке в старых браузерах, его преобразованием также займется Babel. 

К примеру, следующий JSX код:
```jsx
<h1 className=”title”>First React App</h1>
```

Будет транспонирован в уже известный нам синтаксис с `createElement`:

```js
React.createElement(‘h1’, {className: ‘title’, null);
```

#### Пример ####

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello World</title>
    <script src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/babel-standalone@6.15.0/babel.min.js"></script>
  </head>
  <body>
    <div id="root"></div>
    <script type="text/babel">
      ReactDOM.render(
        <h1>Hello, world!</h1>,
        document.getElementById('root')
      );
    </script>
  </body>
</html>
```

Вы, скорее всего, заметили, что появился еще один скрипт.

```html
<script src="https://unpkg.com/babel-standalone@6.15.0/babel.min.js"></script>
```

Это самый простой вариант подключения Babel, но для больших проектов этот путь не подходит и уже придется прибегнуть к сборщикам, к примеру [Webpack](https://webpack.js.org/ "Webpack"){:target="_blank"}{:rel="noopener"} с плагином [babel-loader](https://github.com/babel/babel-loader "babel-loader"){:target="_blank"}{:rel="noopener"}.

Далее, в примере, добавлен JSX скрипт с типом `type="text/babel"` который будет обработан транспилятором и переведен в нативный JavaScript.

Как видно, в функцию `render` передается код `<h1>Hello, world!</h1>`. Данный код не сможет работать в браузере без постобработки.

### Резюме ###

На первый взгляд, смесь JS и HTML может показаться абсурдным, но в дальнейшем, при разработке крупных приложений на React, преимущество за JSX будет очевидным. 
Перечислим основные плюсы:
-	Меньше кода
-	Проще использовать
-	Оптимизация при компиляции

### Оглавление ###
1. [Введение](/frontend/2018/02/04/react-lessons-introduction/ "Введение")
2. [Функциональное программирование](/frontend/2018/02/18/functional-programming-js/ "Функциональное программирование")
3. [Установка](/frontend/2018/03/11/installing-react-js/ "Установка")
4. [JSX](/frontend/2018/04/17/introduction-jsx/ "JSX")
