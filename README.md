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

Этот подход предпочтителен для .....

Блокирует цикл событий

```js
const numsArray = [0, 1, 2, 3, 4, 5];

const isEven = (number: number) => {
  return number % 2 === 0 ? true : false;
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

Код не блокирует цикл событий поэтому ...

Но если много-чего, то настанет callback-hell

Такой способ реализаций ассинхронных событий устарел после появления Promise


# Contributing
The main purpose of this repository is to continue evolving me and you. You are welcome!
