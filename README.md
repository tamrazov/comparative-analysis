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

В этом примере функция isEven является callback и код синхронный 
Этот подход полезен если вы не делаете ассинхронных операций которые могут занять какое-то время и вернуть непонятный результат

Выподнение этого кода приводит к блокировке цикла событий, да?

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
