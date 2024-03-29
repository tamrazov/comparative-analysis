# Сравнительный анализ подходов к организации взаимодействия в современных spa-приложениях.
В тексте будут затронуты несколько вариантов организации взаимодействия различных частей frontend приложений — это callback-функции (callbacks), промисы (promises), генераторы (yield), async/await, корутины, шина обмена сообщениями (eventemitter) и так раскроем модель акторов в JavaScript. Методы обработки цепочки синхронных/асинхронных событий по следующим критериям и на основе этих критериев определить для каждого, какую область применения он имеет - то есть какие задачи с его помощью удобнее или более эффективные, а какие нет. Каждый ответ будет иметь небольшой пример кода JS, демонстрирующий указанное поведение. Ответ по каждому критерию может содержать два варианта (callback могут использоваться для организации как синхронного, так и асинхронного взаимодействия), в этом случае следует рассматривать оба варианта.

> [!WARNING]
> В данном тексте автор претендует на истину в последней инстанции. Все что здесь сказано, верно по сути и по смыслу!
> Автор настаивает читать статью под альбом Mezzanine - Massive Attack

### Callback/Promises
Сallback - это способ расширения функционала, элемент проектирования функций. Однако callback-функции, как и обычные функции, бывают как синхронные, так и ассинхронные(точнее сказать помогают сделать отложенный вызов при выполнении [ассинхроных операции](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Implementing_a_promise-based_API#using_async_and_await_with_the_alarm_api)).
Синхронные callback-функции нужны для функциональной композиций. Ярким примером является функция pipe, реализация которой может быть исполнена в [синхронном](https://ramdajs.com/docs/#pipe) и [aсинхронном](https://nodejs.org/api/stream.html#streampipelinesource-transforms-destination-options) виде.
```js
const reverse = (string) => string.split('').reverse().join('') // Пример синхронной callback-функции

const uppercase = (string) => strign.toUpperCase(string)

const pipe = (...fns) => (x) => fns.reduсe((v, f) => f(v), x)
```
С помощью такого подхода эффективно решать многие синхронные задачи и код становиться более императивным. Код построенный на callback-функциях в  дает возможность писать локаничный и довольно императивный код. Особенно хорошо такие функции работают в паттерне [частичное применение](https://en.wikipedia.org/wiki/Partial_application) и каррирование.

Ассинхронные же операции выполняются callback функциями для того чтобы вернуть значение из функции. Вместо того, чтобы сразу же вернуть какой-то результат, как делает большинство функций, эти,использующие обратные вызовы функции, требуют время для получения результата, а коллбек будет вызван только после выполнения этой операции. Этот подход нужен для выполнения ассинхронных операции, таких как работа с файловой системой и запросов на сервер.

Давайте представим, вы хотите сначала прочитать файл с файловой системы, а затем после чтения выполнить какую-нибудь операцию, но как наверняка узнать когда закончить чтение файла? Ответ: Никак, наша функция должна исполниться после асинхронной операции чтения.
У нас есть функция readFile, которая читает файл с диск. Далее мы хотим, чтобы после чтения файла появилось сообщение об успехе или не успехе.

```js
readFile('~/Desktop/myFile.txt', log) // Ассинхронная операция

function log(err) {
  if(err) {
    console.error('Ошибка чтения!')
  } else {
    console.log('Файл успешно прочитан!')
  }
}

console.log('Операция чтения начата!')
```

Такой подход к написанию кода на JavaScript пораждает немалое количество проблем, такие как [callback-hell](http://callbackhell.ru/). Решение нашлось в Promise, а далее и в async/await, о последних немного позже. Не буду подробно останавливаться на этом. Но callback, Promise, async/await используются в js-коде очень часто, и причем повсеместно.
Очень важно упомянуть здесь про statefull/stateless. Подход statefull в JavaScript говорит о том, что функция которую мы создаем **использует** внешнее состояние которое мы инициальзировали выше. А именно она изменяет наше состояние. Подход stateless обратный - функция которую мы создаем **не использует** внешнее состояние которое мы инициальзировали выше.

```js
const state = 1;

const increment = () => {
  return state += 1;
}

increment(); // 2
```

```js
const state = 1;

const decrement = (num) => {
  return num += 1;
}

decrement(state); // 2
```
Попробуем разобрать middleware redux-thunk, как структуру построенную на callback-функциях, которая помогает нам исполнять ассинхронные запросы в react-redux приложениях с архитектурой flux. Вот исходный код функции:

```js
function createThunkMiddleware(extraArgument) {
  const middleware =
    ({ dispatch, getState }) =>
    next =>
    action => {
      if (typeof action === 'function') {
        return action(dispatch, getState, extraArgument)
      }

      return next(action)
    }
  return middleware
}
```

Итак, сама по себе библиотека redux-thunk небольшая, главная функция createThunkMiddleware умещается в 17 строк. В ней совмешается подходы синхронного коллбека ввиде принимаемого диспатч, далее есть подход stateless, так как функция не использует никакое внешннее состояние, а также pull-подход(про pull/push немного позже) так как, сама выбирает в какой момент использовать данные.

### Yield (Генераторы) и Coroutine(Корутины)
Корутины(сопрограммы) - большая часть языков программирования. Их применение тесно связано с паттерном Producer/Consumer. Можете почитать про эти концепции. В JavaScripte принято использовать только часть констукций корутин, а именно генераторы. Последние являются только частью корутин, а именно применяют концепцию producer, т.е выдают данные наружу программы/функции при yield. Генераторы очень странная и загадочная часть языка JS. Некоторые разработчики видят их сложными. Вы можете быть успешным разработчиком и никогда не чувстовать необходимости в их использовании. Генераторы низкоуровневая конструкция, они как будто инструменты для создания инструментов, которые решают наши повседневные проблемы, но если смотреть на них изолированно, может показаться что они вообще не нужны. Однако это не так.

Генераторы можно использовать для ленивых итераций. Если вы знакомы с [протоколами перебора](https://developer.mozilla.org/ru/docs/Web/JavaScript/Reference/Iteration_protocols), этот функционал появился в 2015 году, тогда вы знаете что они тоже относятся к низкоуровневым конструкция. Эти протоколы говорят движку JS, что данный объект можно итерировать или использовать spread оператор для него. 

```js
function* culturalAchievements() {
  yield 'Amazing professional people';
  yield 'The lovely office';
  yield 'Friendly atmosphere';
}

for (achievement of culturalAchievements()) {
    console.log(`Axenix is known for: ${achievement}`);
}
```
Я не буду вдоваться в подробности генераторов, они могут быть очень полезны, с их помощью можно переписать множество функций высшего порядка. Попробуем переписать функцию filter из примера коллбеков и посмотрим на разницу подходов.
```js
function* filter(predicate, array) {
  for (value of array) {
    if (predicate(value)) {
      yield value
    }
  }
}
console.log(...filter()) // Поскольку функции генераторы возвращает итераторы, то используя оператор spread мы получим нужный нам массив
```
Так например, стейт-менеджер redux-saga, использует для выполнения ассинхроных операций генераторы. Это очень удобно, и делает код визуально синхронным, как и async/await. Код который может выглядеть так:
```js
fetchProducts(url).then((val) => {
  console.log(val)
})
```
будет выглядеть так:
```js
let value = fetchProducts(url)
console.log(value)
```

Вот код из под капота redux saga:
```js
function next(arg, isErr) {
  try {
    let result
    if (isErr) {
      result = iterator.throw(arg)
      // user handled the error, we can clear bookkept valuesadfsf
      sagaError.clear()
    } else if (shouldCancel(arg)) {
      /**
        getting TASK_CANCEL automatically cancels the main task
        We can get this value here
        - By cancelling the parent task manually
        - By joining a Cancelled task
      **/
      mainTask.status = CANCELLED
      /**
        Cancels the current effect; this will propagate the cancellation down to any called tasks
      **/
      next.cancel()
      /**
        If this Generator has a `return` method then invokes it
        This will jump to the finally block;sdfllk
      **/
      result = is.func(iterator.return) ? iterator.return(TASK_CANCEL) : { done: true, value: TASK_CANCEL }
    } else if (shouldTerminate(arg)) {
      // We get TERMINATE flag, i.e. by taking from a channel that ended using `take` (and not `takem` used to trap End of channels)
      result = is.func(iterator.return) ? iterator.return() : { done: true }
    } else {
      result = iterator.next(arg)
    }

    if (!result.done) {
      digestEffect(result.value, parentEffectId, next)
    } else {
      /**
        This Generator has ended, terminate the main task and notify the fork queue
      **/
      if (mainTask.status !== CANCELLED) {
        mainTask.status = DONE
      }
      mainTask.cont(result.value)
    }
  } catch (error) {
    if (mainTask.status === CANCELLED) {
      throw error
    }
    mainTask.status = ABORTED

    mainTask.cont(error, true)
  }
}
```
Преимущества redux saga, но надо помнить что все эти преимущества обоснованы функциями-генераторами. В очередной раз убеждаясь в том что они есть инструменты для инструментов.
1. DOM: jQuery императивный / React декларативный
2. Monads: IO императивный / Free декларативный (еще одна констукция функционального программирвоания, рекомендую почитать)
3. Redux effects: redux-thunk императивный / redux-saga декларативный
### EventEmitter
Концепция pull/push систем основываются на паттерне Producer/Consumer. Довольно широко эти подходы используются в рективном программировании. Эти два подхода описывают как производитель данных (Producer) может взаимодействовать с потребителем данных (Consumer).

Pull системы - это системы в которых Consumer сам определяет как и в какой момент использовать данные, Producer не знает куда и когда будут доставлены данные. Каждая функция JS - это pull система. Функция является источником данных, а код который ее вызывает, является потребителем данных извлекая(pulling) единственно возвращаемое значение из этой функции.

Функции, которые я описывал выше, итераторы и генераторы, также являются pull системами.[pull and push](http://reactivex.io/rxjs/manual/overview.html)

```js
const producer = () => ({ name: 'Alex', age: 24 })
const consumer = () => { let state = producer() }
```

В системах Push Производитель определяет, когда отправлять данные Потребителю. Потребитель не знает, когда он получит эти данные. Промисы — наиболее распространенный тип системы Push в JavaScript на сегодняшний день. Промис (Производитель) предоставляет разрешенное значение зарегистрированным обратным вызовам (Потребителям), но, в отличие от функций, именно Промис отвечает за точное определение того, когда это значение «проталкивается» в обратные вызовы.

```js
let producer = () => new Promise((resolve, reject) => {
  let count = 0;
  setInterval(() => {
    resolve(count++);
  }, 1000);
});

let consumer = data => { console.log(data); };

producer().then(consumer) // 0
```

Итак, на последок попробую рассказать про еще один подход в проектировании систем. Это EventEmitter - это шаблон проектирования. О нем достаточно много написано, и это подход используется в реактивном программировании фреймворк RxJS. Однако я попробую разобрать его. Как я писал выше, pull/push системы довольно частые конструкции в JavaScript. Концепция библиотеки React использует подход pull архитектуры. Это означает что когда React нужно вызвать функцию render, это вызовет рендер всех дочернир компонентов реакта. Это вернет новое предсталение которое потом реакт согласовывать с помощью React.reconcilation. Далее измениния будут преминены на DOM. В этом и состоит pull концепция, в данном случае React, потому что именно он запрашивает новое состояние.

Однако, можно воспользоваться push архитектурой, для того чтобы подписывать компоненты на состояние. Этот подход позволит не только увеличить производительность, но и сделает управление состоянием более декларативным, чтобы можно было читать состояния сверху вниз.

### Actor model


# Contributing
The main purpose of this repository is to continue evolving me and you. I'll accept any help. You are welcome!
<!-- 
Далее давайте расмотрим такую ситуацию, например, ....

```js
const array = [1, 2, 3, 4, 5]


```

такой подход к проетированию функции-генератора называется паттерн трансдюсер -->

