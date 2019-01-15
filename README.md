# ze2019

https://ze2019.com/team

На этом сайте пристутствует счетчик количества участников (людей заполнивших анкету):

![ze counter](https://github.com/dmitriys-lits/ze/blob/master/ze-counter.png?raw=true)

Меня он сразу заинтересовал. Чисто технический интерес, начал смотреть, как он работает, и посыпались подозрения.
Значение счетчика обновлялось раз в какое-то время, а не постоянно, и значение обновления было примерно одинаковое.

Я погуглил немного, и увидел, что уже были высказаны предположения о том, что этот счетчик фейковый, и существует только для того,
чтобы обмануть избирателей, и показать колоссальную поддержку Владимира Зеленского на самом старте избирательной компании.

Один из первых кто заявил об этом:

http://ibigdan.com/2019/01/02/pokuda-est-na-svete-duraki-obmanom-zhit-nam-stalo-byt-s-ruki/

Но, чужие менения, несомненно важны, я же решил проверить все самостоятельно, так как интересуюсь избирательной компанией и выборами в целом.

Я написал простенький скрипт, наподобии такого:

```
root@nodejs-s-1vcpu-1gb-fra1-01:~# cat ./ze/index.js
let counter = 0;

const fetch = require('node-fetch');
const MongoClient = require('mongodb').MongoClient;

// Connection URL
const url = 'mongodb://localhost:27017';

// Database Name
const dbName = 'ze-team';

// Create a new MongoClient
const client = new MongoClient(url);

// Use connect method to connect to the Server
client.connect(function(err) {
  console.log("Connected successfully to server");

  const db = client.db(dbName);
  const updateCounter = async () => {
        const resp = await fetch('https://ze2019.com/storage/counters.json?rand=0.8280749613720932');
        const data = await resp.json();
        db.collection('counter_state')
	  .insert({count: data.volunteer_amount, timestamp: Date.now()});
	console.log('count: ' + data.volunteer_amount + '\n');
  }
  updateCounter();
  setInterval(updateCounter, 60000);
});
```

запустил его на сервере с целью собрать данные и проанализировать рост значения счетчика.

Первый раз стало интересно, что же происходит, спустя всего 2 часа времени - я сделал экспорт данных и построил график в Excel:

![ze counter](https://github.com/dmitriys-lits/ze/blob/master/first-result.png?raw=true)

Как видим, результат абсолютно линейный. То-есть, с одинаковой скоростью на протяжении 2-х часов новые люди заполняли эту анкету.
Сразу стало все ясно, но я решил посмотреть, что же будет с данными за большее количество времени:

И так, как выглядит рост сторонников Владимира Зеленского за период с `08.01.2019 18:54` по `09.01.2019 16:06`:

![ze counter](https://github.com/dmitriys-lits/ze/blob/master/final-result.png?raw=true)

На результирующем графике мы продолжаем видеть график, который никак не связан с реальным числом людей, заполнивших анкету и поддерживающих Владимира Зеленского.
Но зато, что-то новенькое. Мы видим жалкую попытку подогнать фейк под правду, и видим симуляцию сниженной активности роста числа новых сторонников в ночное время.
Лично я думаю, что это результат реакции на посыпавшиеся обвинения в СМИ.

## И что дальше?

А дальше я думаю возможно два варианта развития событий:

1. Зеленский и Ко проигнорируют все подобные обвинения. Такой вариант возможен, но маловероятен, так как скорее всего Владимир Зеленский будет учавствовать в различного рода острых интервью и маловероятно, что у него не спросят ничего по этому поводу.
2. Будет как с 100pudivka (можно найти статью на прекрасном айти). Владимир Зеленский скажет, что он ничего не знал, и что виноваты подрядчики пиарщики/маркетологи, и он их уже давно уволил.

Только вот второй вариант - полний булшит. Все он прекрасно знал, эти цифры счетчика форсятся везде в создаваемый страничках в социальных сетях. А если вдруг, представить сказочный вариант, и что он действительно не знал, то нужен ли нам такой президент, который не знает, чем занимается его команда?

Я надеюсь, что Владимиру Зеленскому не получится обмануть избирателей популяризируя мнение о всенародной любви и поддержке.

## Полная картина

Имеем пять посуточных графика, которые практически идентичные, коэффиценты прироста разные используются. Каждый отдельно взятый график демонстрирует нереальную картину (так как не может быть абсолютно линейного роста) с симмуляцией ночной пониженной активности. Все вместе 5 графика показывают абсолютную периодичность симмуляции прироста, чего в реальном мире тоже не может быть.

**Сутки первые:**

![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/day1.png)


**Сутки вторые:**

![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/day2.png)


**Сутки третьи:**

![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/day3.png)


**Сутки четвертые:**

![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/day4.png)


**Сутки пятые:**

![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/day5.png)

**Сутки шестые:**

![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/day6.png)

**Все вместе (5 суток):**
![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/96_hours_result.png)

## Исходные данные

Собранные данные можно получить здесь:

[raw_data.csv](https://github.com/dmitriys-lits/ze/blob/master/raw_data.csv?raw=true)

## Обновления (снизу вверх)

**12.01.2019 18:54 (данные за 96 часов):**
![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/96_hours_result.png)

**11.01.2019 16:40:**
![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/11_01_2019_-_16_40_result.png)

**10.01.2019 18:54 (данные за 48 часов):**
![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/48hours_result.png)

**10.01.2019 13:00:**
![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/10_01_2019_-_13_00_result.png)

**10.01.2019 00:36:**

![ze counter](https://raw.githubusercontent.com/dmitriys-lits/ze/master/10_01_2019_-_00_36_result.png)
