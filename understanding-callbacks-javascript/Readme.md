***Оригинал статьи:*** https://medium.freecodecamp.com/understanding-asynchronous-javascript-callbacks-through-household-chores-e3de9a1dbd04#.s6e4vtxtx

# Понимаем асинхронные обратные вызовы в Javascript с помощью домашних дел

### Если вы когда-нибудь стирали, то вы поймете как работают обратные вызовы (callbacks).

На днях я прочитал блестящую, веселую и легкую для понимания статью [Кевина Кононенко](https://medium.com/@kevink) про MVC-фреймворки. Он объясняет Модель-Представление-Контроллерную парадигму через заказ напитков в баре, и это было одно из лучших объяснений в программировании!

Я действительно оценил её [статью], потому что она была написана без претензий на элитизм, и это заставило меня задаться вопросом, почему многие другие опытные программисты не могут помочь новичкам без высокомерного отношения?

Я преподаю английский в Северной Корее в данный момент, и мы, учителя, должны думать как Кевин всё время. Сегодня крайне неодобрительно подробно объяснять грамматические концепции, поэтому хорошие учителя стараются контекстуализировать цель языка (т.е грамматику или словарный запас они хотят учить) с помощью историй, фильмов, музыки и картинок.

Эта методология преподавания была под влиянием британских лингсвистов в 1980-е годы, на которой была обоснована педагогика современного иностранного языка сегодня. Может быть тоже самое происходит прямо сейчас для программного образования!

Кевин пошел по недосягаемому для следования пути, но я хотел бы объяснить как работают асихронные функции обратного вызова в Javascript через контекст выполнения обычных домашних дел.

## Синхронный милый-сделай список

Крикните моей жене, которая выполняла двойную работу по дому, в то время пока я учусь кодированию. Я должен ей много времени!

Обычно я помогаю по дому в воскресенье, и ее милый-сделай список для меня выглядит так:

1. Постирай белье
2. Искупай собаку
3. Отсортируй мусор
4. Сбалансируй бюджет
5. Сообрази что мы сделаем на ужин

**Техническая сторона:** по сути, Javascript это синхронный язык программирования, т.е. он работает с одной строкой кода одновременно. Он не может перейти к следующей строке кода, пока не будет выполнена текущая строка. Рассмотрим следующий пример:

```Javascript
function syncChores() {
  console.log('Постирай белье');
  console.log('помой собаку');
  console.log('отсортируй мусор');
}
syncChores();
/* Результат отображается в том же порядке, в каком и был записан:
   Постирай белье
   помой собаку
   отсортируй мусор
*/

```

А теперь представьте если я выполнял бы свою работу синхронно в реальной жизни. Что произойдет? Как бы это выглядело?

Если вы вернетесь к моему списку, то вы увидите, что стирка -- это первый пункт. Обычно это занимает около 35 минут до финиша в типичном цикле стирки и еще 45 минут для ожидания пока постиранное высушится. Так что целых 80 минут я бы просто сидел и ничего не делал, не занимаясь другими делами, так как я синхронно жду когда закончится стирка.

Вот как это выглядит в псевдокоде:

```Javascript
function doLaundry() { // нужно постирать
  startWashCycle(); // старт цикла стирки
  switchToDryer(); // переключиться на сушку
  foldAndIronClothes(); // собрать и поутюжить белье
}
function washDog() {
  // вообразите какой-либо код моющий собаку
}
function sortRecycling() {
  // и вообразите сортирующий мусор код здесь
}
doLaundry(); // Стираем

// Теперь ждите целых 80 минут прежде чем закончить другие функции
// Теперь я могу наконец вымыть свою собаку!

washDog(); // мою собаку
sortRecycling(); // сортирую мусор

```

Не очень продуктивно, правда? В реальной жизни занятые взрослые люди будут заниматься этими делами асинхронно, т.е. они начнут со стирки и продолжат делать другие задачи из списка, и вернутся к стирке только после того, как она завершится.

Это действие возвращения обратно к стирке, когда она будет выполнена, аналогично функциям обратного вызова (callback function) в Javascript, и наша стиральная машинка, в буквальном смысле, вызовет нас назад со звонком или гудком. Это позволяет нам пойти и заняться другими делами, и продолжить стирку когда она будет выполнена.

###  Асинхронный милый-сделай лист

Давайте снова выполним наши дела, но сделаем это асинхронно. Как бы это выглядело в псевдокоде?

```Javascript

function doLaundry(callback) {
  // вообразите код который начинает цикл стирки
  // требуется 80 минут чтобы завершить цикл стирки
  callback(err, cleanLaundry);
}
doLaundry(function(err, cleanLaundry) {
  // иногда наша стиральная машина ломается
  // лучше обработайте эту возможную ошибку
  if (err) throw err;
  // если нет никакой ошибки, переключаемся на сушку белья после того, как постиралось белье
  // Tada! Наш обратный звонок(вызов) оповестит нас когда стирка завершится!

  switchToDryer(cleanLaundry);
});
// Пока мы ждем, JavaScript может выполнить это дело сейчас!
washDog();
// еще осталось время для других дел!
sortRecycling();
// Следующий пункт будет неопределен(undefined) потому что он еще не выполнен
console.log(cleanLaundry);
// Теперь стирка выполнена!
// Давайте вернемся назад и переключися на сушку белья
// Белье высушено. Давайте продолжим выполнять другие наши дела.
// Таня будет впечатлена моей производительностью!
balanceBudget();

```

Как и в статье Кевина, эта статья только проясняет понятие обратных вызовов. Если вы хотите большего практического руководства, прочтите [Ад обратных вызовов.](http://callbackhell.com/)

### Ваша очередь

Это поможет вам, если вы сможете применить абстрактные концепции в реальных ситуациях. Вы можете подумать о том, что вы делаете дома, в школе или на работе, что напоминало бы синхронный и асинхронный код? Напишите это в комментариях ниже!