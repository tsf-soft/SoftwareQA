## Тестирование API

В этом разделе я кратко демонстрирую пример интеграционного тестирования, а именно умение тестировать API приложений с использованием инструмента Postman.
В примере я демонстрирую владение JavaScript в пределах фреймворка Chai, встроенного в Postman, а также использование внутри Postman пользовательских переменных окружения.

<br/>

### 1. Тестирование API на предмет наличия учётных данных у конкретного пользователя

![Проверка наличия пользователя и учётных данных у него](https://github.com/tsf-soft/SoftwareQA/assets/6228605/91e51885-3214-4eac-b4e5-d9448051259a)
_<p>Рисунок 1. Скриншот Postman-клиента с открытым тестом наличия учётки пользователя</p>_

Ниже приведён JavaScript-код показанного на рисунке 1 теста.

```JavaScript

// Карышев Е.Н. июнь 2023г.
// Это исполнение дополнительного задания (со звёздочкой)
// На всякий случай поясню, если не понятно, "что здесь происходит".
// По заданию, нужно сохранить в переменных коллекции следующие свойства 10-ого юзера: id, first_name, email
if (pm.response.to.have.status(200)) {                          // я запрашиваю у api только нужного мне юзера, если он есть
    let jsonData = pm.response.json();
    let myUsr = jsonData.data;                                  // выбираю нужный мне элемент-объект (освобождаюсь от массива)
    delete myUsr.last_name;                                     // убираю лишние свойства объекта
    delete myUsr.avatar;
    pm.collectionVariables.set('credentials', myUsr)            // сохраняю все нужные по заданию свойства - одним объектом в переменной коллекции credentials
    console.log(pm.collectionVariables.get('credentials'));     // демонстрирую, что всё работает
}else {
    console.log("Нифига нет такого пользователя с ID" + pm.collectionVariables.get('ourUser'));
}
```
_<p>Листинг-1. Тестирование наличия учётных данных у конкретного пользователя</p>_

<br/>

### 2. Тестирование API на корректность его работы при частичном изменении учётных данных пользователя

![Проверка корректности изменения учётных данных](https://github.com/tsf-soft/SoftwareQA/assets/6228605/bcced7bf-b864-4965-9e22-2a35add0e217)

_<p>Рисунок 2. Скриншот Postman-клиента с открытым тестом корректности изменений учётных данных</p>_

Ниже также приведён JavaScript-код показанного на рисунке 2 теста.

```JavaScript

// Карышев Е.Н. июнь 2023г.
// первый тест - "Статус завершения запроса ОК"
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

// второй тест - поле "Дата обновления - не пустое"
pm.test("Date is not empty", function () {
    let jsonData = pm.response.json();
    pm.expect(jsonData.updatedAt).to.not.empty;
});

// третий тест - "Дата обновления - сегодня"
pm.test("The date of update is today", function () {
    let jsonData = pm.response.json();
    const moment = require('moment');
    let myDate =  new Date(jsonData.updatedAt);
    let nowDate = new Date(Date.now())
    pm.expect(moment(myDate).format("YYYY-MM-DD")).to.eql(moment(nowDate).format("YYYY-MM-DD"));
});

```
