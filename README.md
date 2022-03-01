# antiblock
Обход Интернет-цензуры стандартными средствами (SSH-туннелирование)

## Коротко об SSH-туннелировании

Существует множество способов обойти Интернет-цензуру. SSH-туннелирование - один
из них, со своими достоинствами и недостатками. К достоинствам можно
отнести использование стандартной Интернет-инфраструктуры, что минимизирует
возможность блокировки, высокая скорость соединения, отсутствие необходимости
устанавливать сторонее программное обеспечение. К недостаткам - необходимость
ручной настройки.

Настройка состоит из следующих шагов:

1. Подготовка клиента (локального компьютера)
2. Запуск виртуальной машины в локации где нет цензуры
3. Подключение к ней по SSH с созданием туннеля
4. Использование SSH туннеля в качестве прокси

## Подготовка клиента (локального компьютера)

Установка стороннего программного обеспечения не требуется, однако на
ОС Windows необходимо включить компонент - OpenSSH клиент. На MacOS и Linux
SSH клиент установлен по умолчанию.

### Windows: установка OpenSSH клиента

Подробная инструкция: https://spy-soft.net/windows-10-openssh-client/

1. Зайдите в "Параметры > Приложения > Приложения и Возможности > Управление дополнительными компонентами"
2. Убедитесь что в списке нет компонента "OpenSSH Client". Если оно есть,
следующие шаги можно пропустить
4. Нажмите кнопку "Добавить компонент", найдите в списке "OpenSSH Client",
кликните по нему и нажмите кнопку "Установить"
5. После установки закройте все окна и перезагрузите компьютер

### Windows: генерация RSA-ключей

### MacOS и Linux: генерация RSA-ключей

## Запуск виртуальной машины

Простыми словами, виртуальная машина это "виртуальный компьютер" в облаке,
со своей операционной системой, программным обеспечением и данными. Виртуальная
машина может иметь один или несколько IP-адресов, с помощью которых к ней можно
подключиться удаленно. Одна виртуальная машина может обслуживать множество
клиентов, поэтому если кто-либо готов предоставить вам **доступ к виртуальной машине
по SSH соединению**, этот этап можно пропустить.

Для начала нужно определиться с провайдером: они отличаются по цене, сложности
настройки, выбором локаций и т.д. Бесплатных вариантов нет, но есть условно-бесплатные.
Например, Amazon предоставляет одну бесплатную виртуальную машину в первые 12 месяцев;
у других крупных провайдеров тоже есть похожие пакеты. Кроме того, к одной виртуальной
машине можно подключить несколько пользователей.

TODO: (здесь будет список провайдеров, их достоинства и недостатки)

### Digital Ocean

https://ru.wikipedia.org/wiki/DigitalOcean

* Один из самых простых в использовании облачных сервисов
* Цена: от $5 в месяц (первые 2 месяца бесплатно)
* Недостатки: отсутствие интерфейса на русском языке и
история временной блокировки сервиса в России в 2018 году
* Промо-ссылка: https://www.digitalocean.com/try/free-trial-offer

1. Зарегистрироваться (введя адрес эл. почты, или через Google или GitHub)
2. Подтвердить адрес электронной почты, кликнув на ссылку в письме
3. Добавить способ оплаты (банковская карта потребует пре-авторизации на небольшую сумму;
PayPal - депозита в 5 долларов)
4. Создать дроплет (Droplets > Create > Droplets)
5. Выбрать настройки:
  - Choose an image > Distributions > Ubuntu
  - Choose a plan > Basic
  - Regular Intel with SSD
  - $5/mo ($0.007/hour)
  - Choose a datacenter region (Выбрать любой, на ваше усмотрение. Скорость подключения отличается
от региона к региону, но предугадать ее трудно).
  - TODO: SSH key
6. TODO: запуск
