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

Для этого понадобится командная строка. Это стандартное приложение
Windows которое позволяет запускать различные команды в текстовом
терминале. Ввод команд возможен после т.н. "строки приглашения" -
по умолчанию она отображает путь к текущей папке. Запуск команды
осуществляется нажатием "Enter". В командной строке рекомендуется
пользоваться латинской раскладкой клавиатуры (а не русской).

Подробно тут:
https://www.dell.com/support/kbdoc/ru-ru/000130703/the-command-prompt-what-it-is-and-how-to-use-on-a-dell-system

1. Откройте командную строку. Для этого проще всего в главном меню впечатайть `cmd` и нажать "Открыть"
2. С помощью клавиатуры в командной строке введите команду `ssh-keygen` и нажмите Enter несколько раз
пока снова не увидите строку приветствия. Это сгененирует пару RSA-ключей для удаленного подключения -
один открытый и один закрытый ключ. Подробно об SSH-ключах:
https://docs.microsoft.com/ru-ru/windows-server/administration/openssh/openssh_keymanagement
3. Введите команду  `type .ssh\id_rsa.pub` и нажмите Enter (ВНИМАНИЕ: после "type" следует пробел, а за ним - точка.
Обратная косая черта `\` находится над клавишей Enter. Обратите внимание чтобы команда заканчивалась символами `.pub`!)
4. В окне командной строки отобразится открытый ключ - длинный кусок текста начинающийся символами `ssh-rsa` и заканчивающийся
именем компьютера, обычно занимающий несколько строчек в окне командной строки. Его нужно аккуратно выделить мышкой полностью,
и скопировать нажав "Enter".

### MacOS и Linux: генерация RSA-ключей

Для этого понадобится программа "Терминал" - аналог "Командной строки" в Windows.
Это стандартное приложение которое позволяет запускать различные команды в текстовом
терминале. Ввод команд возможен после т.н. "строки приглашения" -
по умолчанию она отображает путь к текущей папке. Запуск команды
осуществляется нажатием "Enter". В терминале рекомендуется
пользоваться латинской раскладкой клавиатуры (а не русской).

1. Поищите в приложениях "Terminal" и запустите его
2. С помощью клавиатуры в командной строке введите команду `ssh-keygen` и нажмите Enter несколько раз
пока снова не увидите строку приветствия. Это сгененирует пару RSA-ключей для удаленного подключения -
один открытый и один закрытый ключ. Подробно об SSH-ключах: https://help.icdc.io/compute/ru/FAQ/SSH/Create1.html
3. Введите команду  `cat ~/.ssh/id_rsa.pub` и нажмите Enter (ВНИМАНИЕ: после "type" следует пробел, а за ним - тильда,
косая черта и точка. Тильда находится слева от клавиши "1"; косая черта - справа, в предпоследнем ряду клавиш.
Обратите внимание чтобы команда заканчивалась символами `.pub`!)
4. В окне терминала отобразится открытый ключ - длинный кусок текста начинающийся символами `ssh-rsa` и заканчивающийся
именем компьютера, обычно занимающий несколько строчек в окне командной строки. Его нужно аккуратно выделить мышкой полностью,
и скопировать, кликнув правой кнопкой мышки и выбрав пункт меню "Копировать".

### Использование полученного открытого SSH-ключа

Открытый ключ необходимо добавить в файл ключей на виртуальной машине (см. ниже), либо
переслать его владельцу виртуальной машины если кто-то готов предоставить вам доступ.
Открытый ключ RSA можно передавать по незащищенным каналам связи (мессенджер, электронная почта),
однако получателю нужо удостовериться что полученный им ключ именно ваш. Не стоит
публиковать ключ в открытом доступе (на форумах, в социальных сетях, и т.д.)

## Запуск виртуальной машины

Простыми словами, виртуальная машина это "виртуальный компьютер" в облаке,
со своей операционной системой, программным обеспечением и данными. Виртуальная
машина может иметь один или несколько IP-адресов, с помощью которых к ней можно
подключиться удаленно. Одна виртуальная машина может обслуживать множество
клиентов, поэтому:

**Если кто-либо готов предоставить вам доступ к виртуальной машине
по SSH соединению, этот этап можно пропустить!**

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
