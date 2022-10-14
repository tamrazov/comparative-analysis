# Comparative-analysis
Comparative analysis of approaches to organizing interaction in application

# Motivation
I am writing this in order to understand this.

# About
In this article, I will touch on several options for organizing the interaction of various parts of our application - these are callback functions (callbacks), generators (yeild) and the message exchange bus (eventemitter).

# How it works

I will try to compare these three methods of processing the chain of asynchronous events according to the following criteria and, based on these criteria, determine for each what field of application it has - that is, which tasks with its help are more convenient or more efficient, and which are not.

![](https://github.com/aleksandrtamrazov/Comparative-analysis/blob/master/img/mainTable.png)

Each answer will have a small sample JS code that demonstrates the specified behavior. The answer for each criterion can contain two options (callbacks can be used to organize both synchronous and asynchronous interaction), in which case both options should be considered.

# Main

Я Саша, пишу про js.

### Всем читающим эту статью здрасте. Сегодня я хотел бы поделиться с вами разбором таблицы. Негативные комментарии, касающиеся моего кода и реализации поддерживаются. Настоятельно рекомендую вам высказаться по поводу всего.

Я попытаюсь сравнить эти три метода обработки цепочки асинхронных событий по следующим критериям и на основе этих критериев определить для каждого, какую область применения он имеет - то есть какие задачи с его помощью удобнее или более эффективные, а какие нет.

Каждый ответ будет иметь небольшой пример кода JS, демонстрирующий указанное поведение. Ответ по каждому критерию может содержать два варианта (обратные вызовы могут использоваться для организации как синхронного, так и асинхронного взаимодействия), в этом случае следует рассматривать оба варианта.

Поехали!!!

#### Callback/Sync

Способ расширения функционала, элемент проектирования

Этот подход предпочтителен для ..... функциональной композиций  

Блокирует цикл событий

```js
const numsArray = [0, 1, 2, 3, 4, 5];

const isEven = (number) => {
  return number % 2 === 0;
}

const result = numsArray.map(isEven) // [true, false, true, false, true, false]
```

В этом примере код синхронный и функция isEven является callback. Этот подход полезен если вы не делаете ассинхронных операций которые могут занять некоторое время и вернуть результат в виде ошибки. Выподнение этого кода приводит к блокировке цикла событий (event loop). Когда мы говорим о блокировке цикла событий, тут подразумевается 

С помощью такого подхода эффективно решать многие синхронные задачи

#### callback/async

Этот подход нужен для выполнения ассинхронных операции, таких как работа с файловой системой и запросов на сервер



```js

const callback = () => console.log('I`m from async callback');

setTimeout(callback, 2000);

console.log('I`m from sync code')

// I`m from sync code
// I`m from async callback

```

Код не блокирует цикл событий поэтому

Но если много-чего, то настанет callback-hell

Такой способ реализаций ассинхронных событий устарел после появления Promise

#### callback/stateful

Подход statefull в JavaScript говорит о том, что функция которую мы создаем использует внешнее состояние которое мы инициальзировали выше.

```js
const state = 1;

const increment = () => {
  return state += 1;
}

increment(); // 2
```

Такой подход в конструировании функций очень наглядно видно при работе с замыканием.

```js
const getMessage = (msg) => {
  const result = msg;

  return function() {
    return result;
  }
}

var logMessage = getMessage('Hello');
console.log(logMessage()); // Hello
```

#### callback/stateless

Подход stateless в JavaScript говорит о том, что функция которую мы создаем не использует внешнее состояние которое мы инициальзировали выше.

```js
const state = 1;

const decrement = (num) => {
  return num += 1;
}

decrement(state); // 2
```

#### callback/pull




#### yald/sync
При помощи reduce мы можем сделать эту операцию, но нам по прежнему надо пройти по всему массиву.

```js
const producs = [
  {id: 1, price: 100},
  {id: 2, price: 200},
  {id: 3, price: 300},
  {id: 4, price: 400}, 
  {id: 5, price: 500}
]

const getProducs = (arr) => {
  return arr.reduce((acc, item) => {
    if (acc.length < 2) {
      acc.push(item);
    }
  }, [])
}
```

Паттерн трансдьюсер может помочь нам не проходится по всему массиву, а остановиться когда мы набрал

```js

```

#### yald/async

Природа генератора распологает к асинх

сделат фетч и возобновить генератор

пример с генератором и коллбек

редак сага сравнить redux-thunk

#### ee/sunc

Rx.js 

Паттерн обсервебр наблюдатель

#### ee/asunc

Rx.js 

Паттерн обсервебр наблюдатель

#### callback statefull/stateless

Коллбэки это стафулл, потому что у них есть состояние

```js
const cb = () => {
  const state = {}; // Это и есть состояние кол-бека 

  // anything
}
```

#### cb/statefull // stateless

генераторы стейфулл у них есть состояние , стейт машина , 

пуш.пулл про то как вызывается код

некс это пулл подход



редак чистый 

состояние тоже сайд эффект




# Contributing
The main purpose of this repository is to continue evolving me and you. I'll accept any help. You are welcome!
