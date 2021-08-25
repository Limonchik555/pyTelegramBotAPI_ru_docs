# <p align="center">pyTelegramBotAPI

<p align="center">Простая, но расширяемая реализация Python для <a href="https://core.telegram.org/bots/api">Telegram Bot API</a>.

[![PyPi Package Version](https://img.shields.io/pypi/v/pyTelegramBotAPI.svg)](https://pypi.python.org/pypi/pyTelegramBotAPI)
[![Supported Python versions](https://img.shields.io/pypi/pyversions/pyTelegramBotAPI.svg)](https://pypi.python.org/pypi/pyTelegramBotAPI)
[![Build Status](https://travis-ci.org/eternnoir/pyTelegramBotAPI.svg?branch=master)](https://travis-ci.org/eternnoir/pyTelegramBotAPI)
[![PyPi downloads](https://img.shields.io/pypi/dm/pyTelegramBotAPI.svg)](https://pypi.org/project/pyTelegramBotAPI/)

  * [Начало.](#начало)
  * [Написание своего первого бота](#написание-своего-первого-бота)
    * [Советы](#советы)
    * [Простой эхо-бот](#простой-эхо-бот)
  * [Общая документация по API](#общая-документация-по-api)
    * [Типы](#типы)
    * [Методы](#методы)
    * [Общее использование API](#общее-использование-api)
      * [Обработчики сообщений](#обработчики-сообщений)
      * [Callback Query Обработчики](#callback-query-обработчик)
      * [Middleware Обработчики](#middleware-обработчик)
      * [TeleBot](#telebot)
      * [Reply markup](#reply-markup)
      * [Inline Mode](#inline-mode)
  * [Расширенное использование API](#расширенное-использование-api)
    * [Асинхронная отправка сообщений](#асинхронная-отправка-сообщений)
    * [Отправка больших текстовых сообщений](#отправка-больших-текстовых-сообщений)
    * [Управление количеством потоков, используемых TeleBot](#управление-количеством-потоков-используемых-telebot)
    * [Механизм слушателя](#механизм-слушателя)
    * [Использование веб-хуков](#использование-веб-хуков)
    * [Логирование](#логирование)
    * [Прокси](#прокси)
  * [Соответствие API](#соответствие-api)
  * [Журнал изменений](#журнал-изменений)
  * [F.A.Q.](#faq)
    * [Bot 2.0](#bot-20)
    * [Как отличить пользователя от чата в message.chat?](#как-отличить-пользователя-от-чата-в-messagechat)
    * [Как я могу справиться с повторяющейся ошибкой ConnectionResetError?](#как-я-могу-справиться-с-повторяющейся-ошибкой-connectionreseterror)
  * [Чат-группа Telegram](#чат-группа-telegram)
  * [Примеры ботов](#примеры-ботов)
  * [Боты, использующие этот API](#боты-использующие-этот-api)

## Начало.

Этот API протестирован с Python 3.6-3.9 и Pypy 3.
Установить библиотеку можно двумя способами:

* Установка с помощью pip (менеджер пакетов Python)*:

```
$ pip install pyTelegramBotAPI
```
* Установка из исходников (требуется git):

```
$ git clone https://github.com/eternnoir/pyTelegramBotAPI.git
$ cd pyTelegramBotAPI
$ python setup.py install
```

Обычно рекомендуется использовать первый вариант.

**Хоть API и готов к использованию, он все еще находится в стадии разработки и имеет постоянные изменения, не забывайте регулярно обновлять его, вызывая `pip install pytelegrambotapi --upgrade`*

## Написание своего первого бота

## Советы

Предполагается, что вы уже [получили токен API с помощью @BotFather](https://core.telegram.org/bots#botfather). Мы назовем этот токен: `TOKEN`.
Кроме того, у вас должны быть базовые знания языка программирования Python и, что более важно, [Telegram Bot API](https://core.telegram.org/bots/api).

### Простой эхо-бот

Класс Telebot (определенный в \ __ init __.py) инкапсулирует все вызовы API в одном классе. Он обеспечивает такие функции, как `send_xyz` (` send_message`, `send_document` и т.д.), а также несколько способов прослушивания входящих сообщений.

Создайте файл под названием `echo_bot.py`. Затем откройте файл и создайте экземпляр класса TeleBot.
```python
import telebot

bot = telebot.TeleBot("TOKEN", parse_mode=None) # Вы можете установить parse_mode по умолчанию. HTML или Markdown
```
*Примечание: Убедитесь, что на самом деле заменили токен своим собственным токеном API.*

После этого действия нам нужно зарегистрировать некоторые так называемые обработчики сообщений. Обработчики сообщений определяют фильтры, которым необходимо пройти сообщение. Если сообщение проходит фильтр, это называется декоратор функции, и входящее сообщение передается как аргумент.

Давайте определим обработчик сообщений, который обрабатывает входящие команды `/start` и `/help`.
```python
@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
	bot.reply_to(message, "Привет, как дела?")
```
Функция, украшенная обработчиком сообщений __может иметь произвольное имя, но должена иметь только один параметр (message)__.

Добавим еще один обработчик:
```python
@bot.message_handler(func=lambda m: True)
def echo_all(message):
	bot.reply_to(message, message.text)
```
Этот декоратор повторяет все входящие текстовые сообщения обратно отправителю.  Он использует lambda-функцию для проверки сообщения.  Если lambda возвращает True, сообщение обрабатывается декорированной функцией.  Поскольку мы хотим, чтобы все сообщения обрабатывались этой функцией, мы просто всегда возвращаем True.

*Примечание: все обработчики тестируются в том порядке, в котором они были объявлены*

Теперь у нас есть базовый бот, который отвечает на команды "/start" и "/help", и повторяет отправленные сообщения. Чтобы запустить бота, добавьте в наш исходный файл следующее:
```python
bot.polling()
```
Хорошо, вот и все! Наш исходный файл теперь выглядит так:
```python
import telebot

bot = telebot.TeleBot("TOKEN")

@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
	bot.reply_to(message, "Привет, как дела?")

@bot.message_handler(func=lambda message: True)
def echo_all(message):
	bot.reply_to(message, message.text)

bot.polling()
```
Чтобы запустить бота, просто откройте терминал и введите `python echo_bot.py`. Проверьте бота, отправив команды ("/start" и "/help") и произвольные текстовые сообщения.

## Общая документация по API

### Типы

Все типы определены в types.py. Они полностью соответствуют [определению типов в Telegram API](https://core.telegram.org/bots/api#available-types), за исключением поля `from`, которое переименовано в `from_user` (поскольку `from` является зарезервированным токеном Python). Таким образом, к таким атрибутам, как `message_id`, можно получить доступ напрямую через `message.message_id`. Обратите внимание, что `message.chat` может быть экземпляром `User` или `GroupChat`. (Смотрите [как отличить пользователя от чата в message.chat?](#как-отличить-пользователя-от-чата-в-messagechat)).

Объект Message также имеет атрибут `content_type`, который определяет тип сообщения. content_type может быть одной из следующих строк:
`text`, `audio`, `document`, `photo`, `sticker`, `video`, `video_note`, `voice`, `location`, `contact`, `new_chat_members`, `left_chat_member`, `new_chat_title`, `new_chat_photo`, `delete_chat_photo`, `group_chat_created`, `supergroup_chat_created`, `channel_chat_created`, `migrate_to_chat_id`, `migrate_from_chat_id`, `pinned_message`.

Вы можете использовать несколько типов в одной функции. Пример:

```content_types=["text", "sticker", "pinned_message", "photo", "audio"]```

### Методы

Все [API методы](https://core.telegram.org/bots/api#available-methods) находятся в классе TeleBot. Они переименованы в соответствии с общими соглашениями об именах Python. Например: `getMe` переименован в `get_me`, а `sendMessage` в `send_message`.

### Общее использование API

Ниже описаны некоторые общие варианты использования API.

#### Обработчики сообщений
Обработчик сообщений - это функция, украшенная декоратором `message_handler` экземпляра TeleBot. Обработчики сообщений состоят из одного или нескольких фильтров.
Каждый фильтр должен возвращать True для определенного сообщения, чтобы обработчик сообщения имел право обрабатывать это сообщение. Обработчик сообщений объявляется следующим образом (при условии, что `bot` является экземпляром TeleBot):
```python
@bot.message_handler(filters)
def function_name(message):
	bot.reply_to(message, "Это обработчик сообщений")
```
`function_name` не привязан к каким-либо ограничениям. Для обработчиков сообщений разрешено любое имя функции. Функция должна принимать не более одного аргумента, который будет `message` и который функция должна обработать.
`filters` - это список аргументов(ключевых слов).
Фильтр объявляется следующим образом: `name=argument`. У одного обработчика может быть несколько фильтров.
TeleBot поддерживает следующие фильтры:

|name|argument(s)|Condition|
|:---:|---| ---|
|content_types|list of strings (default `['text']`)|`True` if message.content_type is in the list of strings.|
|regexp|a regular expression as a string|`True` if `re.search(regexp_arg)` returns `True` and `message.content_type == 'text'` (See [Python Regular Expressions](https://docs.python.org/2/library/re.html))|
|commands|list of strings|`True` if `message.content_type == 'text'` and `message.text` starts with a command that is in the list of strings.|
|func|a function (lambda or function reference)|`True` if the lambda or function reference returns `True`

Вот несколько примеров использования фильтров и обработчиков сообщений:

```python
import telebot
bot = telebot.TeleBot("TOKEN")

# Обрабатывает все текстовые сообщения, содержащие команды "/start" или "/help".
@bot.message_handler(commands=['start', 'help'])
def handle_start_help(message):
	pass

# Обрабатывает все отправленные документы и аудиофайлы
@bot.message_handler(content_types=['document', 'audio'])
def handle_docs_audio(message):
	pass

# Обрабатывает все текстовые сообщения, соответствующие регулярному выражению
@bot.message_handler(regexp="SOME_REGEXP")
def handle_message(message):
	pass

# Обрабатывает все сообщения, для которых lambda возвращает True
@bot.message_handler(func=lambda message: message.document.mime_type == 'text/plain', content_types=['document'])
def handle_text_doc(message):
	pass

# Что также можно определить как:
def test_message(message):
	return message.document.mime_type == 'text/plain'

@bot.message_handler(func=test_message, content_types=['document'])
def handle_text_doc(message):
	pass

# Обработчики могут быть сгруппированы для создания функции, которая будет вызываться, если подходит любой message_handler.
# Этот обработчик будет вызываться, если сообщение начинается с "/hello" или ​​это какой-то смайлик.
@bot.message_handler(commands=['hello'])
@bot.message_handler(func=lambda msg: msg.text.encode("utf-8") == SOME_FANCY_EMOJI)
def send_something(message):
    pass
```
**Важно: все обработчики тестируются в том порядке, в котором они были объявлены**

#### Edited Message handlers
Обработка отредактированных сообщений
`@bot.edited_message_handler(filters) # <- передает объект типа сообщения вашей функции`

#### channel_post_handler
Обработка сообщений в канале
`@bot.channel_post_handler(filters) # <- передает объект типа сообщения вашей функции`

#### edited_channel_post_handler
Обработка отредактированных сообщений канала
`@bot.edited_channel_post_handler(filters) # <- передает объект типа сообщения вашей функции`

#### Callback Query Обработчик
Обработка запросов обратного вызова
```python
@bot.callback_query_handler(func=lambda call: True)
def  test_callback(call): # <- передает объект типа CallbackQuery в вашу функцию
    logger.info(call)
```

#### Inline Handler
Обработка встроенных запросов
`@bot.inline_handler() # <- передает объект типа InlineQuery в вашу функцию` 

#### Chosen Inline Handler
Обработка выбранных встроенных результатов
`@bot.chosen_inline_handler() # <- передает объект типа ChosenInlineResult в вашу функцию`

#### Shipping Query Handler
Обработка запросов на доставку
`@bot.shipping_query_handeler() # <- передает объект типа ShippingQuery в вашу функцию`

#### Pre Checkout Query Handler
Обработка запросов до проверки
`@bot.pre_checkout_query_handler() # <- передает объект типа PreCheckoutQuery в вашу функцию`

#### Poll Handler
Обработка обновлений опросов
`@bot.poll_handler() # <- передает объект типа Poll в вашу функцию`

#### Poll Answer Handler
Обработка ответов на опрос
`@bot.poll_answer_handler() # <- передает объект типа PollAnswer в вашу функцию`

#### My Chat Member Handler
Обработка обновлений статуса участника бота в чате
`@bot.my_chat_member_handler() # <- передает объект типа ChatMemberUpdated в вашу функцию`

#### Chat Member Handler
Обработка обновлений статуса участника чата в чате
`@bot.chat_member_handler() # <- передает объект типа ChatMemberUpdated в вашу функцию`

*Примечание: обновления "chat_member" по умолчанию не запрашиваются. Если вы хотите разрешить все типы обновлений, установите для `allowed_updates` в `bot.polling()` / `bot.infinity_polling()` значение `util.update_types`*


#### Middleware Обработчик

Middleware обработчик - это функция, которая позволяет вам изменять запросы или контекст бота, когда они проходят через Telegram боту. Вы можете представить middleware обработчик как цепочку логических соединений, обрабатываемую до того, как будут обработаны другие обработчики. Обработка middleware обработчиков по умолчанию отключена, включите ее, установив `apihelper.ENABLE_MIDDLEWARE = True`. 

```python
apihelper.ENABLE_MIDDLEWARE = True

@bot.middleware_handler(update_types=['message'])
def modify_message(bot_instance, message):
    #  изменение сообщения до того, как оно достигнет любого другого обработчика
    message.another_text = message.text + ':changed'

@bot.message_handler(commands=['start'])
def start(message):
    # сообщение уже изменено, когда оно достигает обработчика сообщений
    assert message.another_text == message.text + ':changed'
```
Есть и другие примеры использования обработчика middleware в директории [examples/middleware](examples/middleware).

#### TeleBot
```python
import telebot

TOKEN = '<token_string>'
tb = telebot.TeleBot(TOKEN)	# создать новый объект TeleBot

# После вызова этой функции TeleBot начинает опрашивать серверы Telegram на предмет новых сообщений.
# - none_stop: True / False (по умолчанию False) - не прекращает опрос при получении ошибки от серверов Telegram
# - interval: True / False (по умолчанию False) - интервал между запросами на опрос
#           Примечание: изменение этого параметра снижает время отклика бота.
# - timeout: integer (по умолчанию 20) - тайм-аут в секундах для длительного опроса.
# - allowed_updates: string (по умолчанию None) - список типов обновлений для запроса
tb.polling(none_stop=False, interval=0, timeout=20)

# getMe
user = tb.get_me()

# setWebhook
tb.set_webhook(url="http://example.com", certificate=open('mycert.pem'))
# unset webhook
tb.remove_webhook()

# getUpdates
updates = tb.get_updates()
updates = tb.get_updates(1234,100,20) #get_Updates(offset, limit, timeout):

# sendMessage
tb.send_message(chat_id, text)

# editMessageText
tb.edit_message_text(new_text, chat_id, message_id)

# forwardMessage
tb.forward_message(to_chat_id, from_chat_id, message_id)

# Все функции send_xyz, которые могут принимать файл в качестве аргумента, также могут принимать file_id вместо файла.
# sendPhoto
photo = open('/tmp/photo.png', 'rb')
tb.send_photo(chat_id, photo)
tb.send_photo(chat_id, "FILEID")

# sendAudio
audio = open('/tmp/audio.mp3', 'rb')
tb.send_audio(chat_id, audio)
tb.send_audio(chat_id, "FILEID")

## sendAudio с продолжительностью, исполнителем и названием.
tb.send_audio(CHAT_ID, file_data, 1, 'eternnoir', 'pyTelegram')

# sendVoice
voice = open('/tmp/voice.ogg', 'rb')
tb.send_voice(chat_id, voice)
tb.send_voice(chat_id, "FILEID")

# sendDocument
doc = open('/tmp/file.txt', 'rb')
tb.send_document(chat_id, doc)
tb.send_document(chat_id, "FILEID")

# sendSticker
sti = open('/tmp/sti.webp', 'rb')
tb.send_sticker(chat_id, sti)
tb.send_sticker(chat_id, "FILEID")

# sendVideo
video = open('/tmp/video.mp4', 'rb')
tb.send_video(chat_id, video)
tb.send_video(chat_id, "FILEID")

# sendVideoNote
videonote = open('/tmp/videonote.mp4', 'rb')
tb.send_video_note(chat_id, videonote)
tb.send_video_note(chat_id, "FILEID")

# sendLocation
tb.send_location(chat_id, lat, lon)

# sendChatAction
# action_string может быть одной из следующих строк: 'typing', 'upload_photo', 'record_video', 'upload_video',
# 'record_audio', 'upload_audio', 'upload_document' or 'find_location'.
tb.send_chat_action(chat_id, action_string)

# getFile
# Скачать файл очень просто
# Возвращает объект File
import requests
file_info = tb.get_file(file_id)

file = requests.get('https://api.telegram.org/file/bot{0}/{1}'.format(API_TOKEN, file_info.file_path))


```
#### Reply markup
Все функции TeleBot - `send_xyz` принимают необязательный аргумент `reply_markup`. Этот аргумент должен быть экземпляром `ReplyKeyboardMarkup`, `ReplyKeyboardRemove` или `ForceReply`, которые определены в types.py.

```python
from telebot import types

# Использование класса ReplyKeyboardMarkup
# Он может принимать следующие необязательные аргументы:
# - resize_keyboard: True/False (по умолчанию False)
# - one_time_keyboard: True/False (по умолчанию False)
# - selective: True/False (по умолчанию False)
# - row_width: integer (по умолчанию 3)
# row_width используется в сочетании с функцией add().
# Он так же определяет, сколько кнопок уместится в каждой строке, прежде чем перейти к следующей.
markup = types.ReplyKeyboardMarkup(row_width=2)
itembtn1 = types.KeyboardButton('a')
itembtn2 = types.KeyboardButton('v')
itembtn3 = types.KeyboardButton('d')
markup.add(itembtn1, itembtn2, itembtn3)
tb.send_message(chat_id, "Выберите одну букву:", reply_markup=markup)

# или добавьте KeyboardButton по одной строке за раз:
markup = types.ReplyKeyboardMarkup()
itembtna = types.KeyboardButton('a')
itembtnv = types.KeyboardButton('v')
itembtnc = types.KeyboardButton('c')
itembtnd = types.KeyboardButton('d')
itembtne = types.KeyboardButton('e')
markup.row(itembtna, itembtnv)
markup.row(itembtnc, itembtnd, itembtne)
tb.send_message(chat_id, "Выберите одну букву:", reply_markup=markup)
```
Последний пример дает такой результат:

![ReplyKeyboardMarkup](https://farm3.staticflickr.com/2933/32418726704_9ef76093cf_o_d.jpg "ReplyKeyboardMarkup")

```python
# скрывает ранее отправленный ReplyKeyboardMarkup
# Принимает необязательный выборочный аргумент (True/False, по умолчанию False)
markup = types.ReplyKeyboardRemove(selective=False)
tb.send_message(chat_id, message, reply_markup=markup)
```

```python
# ForceReply: заставляет пользователя ответить на сообщение
# Принимает необязательный выборочный аргумент (True/False, по умолчанию False)
markup = types.ForceReply(selective=False)
tb.send_message(chat_id, "Напишите мне еще одно слово:", reply_markup=markup)
```
ForceReply:

![ForceReply](https://farm4.staticflickr.com/3809/32418726814_d1baec0fc2_o_d.jpg "ForceReply")

### Inline Mode

Больше информации о [Inline mode](https://core.telegram.org/bots/inline).

#### inline_handler

Теперь вы можете использовать inline_handler для получения встроенных запросов в Telebot.

```python

@bot.inline_handler(lambda query: query.query == 'text')
def query_text(inline_query):
    # Сообщение запроса - это текст
```


#### chosen_inline_handler

Используйте chosen_inline_handler чтобы получить chosen_inline_result в telebot. Незабудьте добавить команду /setinlinefeedback в @Botfather.

Больше информации: [collecting-feedback](https://core.telegram.org/bots/inline#collecting-feedback)

```python
@bot.chosen_inline_handler(func=lambda chosen_inline_result: True)
def test_chosen(chosen_inline_result):
    # Обработать все chosen_inline_result.
```

#### answer_inline_query

```python
@bot.inline_handler(lambda query: query.query == 'text')
def query_text(inline_query):
    try:
        r = types.InlineQueryResultArticle('1', 'Result', types.InputTextMessageContent('Сообщение о результате.'))
        r2 = types.InlineQueryResultArticle('2', 'Result2', types.InputTextMessageContent('Сообщение о результате 2.'))
        bot.answer_inline_query(inline_query.id, [r, r2])
    except Exception as e:
        print(e)

```
### Работа с сущностями:
Этот объект представляет собой одну особую сущность в текстовом сообщении. Например, хэштеги, имена пользователей, URL-адреса и т.д.

Атрибуты:
* `type`
* `url`
* `offset`
* `length`
* `user`


**Вот пример: **`message.entities[num].<attribute>`<br>
Здесь `num` - это номер объекта или порядок объектов в ответе, если в ответе/сообщении есть несколько объектов.<br>
`message.entities` возвращает список объектов объекта.<br>
`message.entities[0].type` даст тип первой сущности<br>
Смотрите [Bot Api](https://core.telegram.org/bots/api#messageentity) для дополнительных деталей

## Расширенное использование API

### Использование локального сервера API ботов
Начиная с версии 5.0 Bot API, у вас есть возможность запускать собственные [Локальные Bot API Сервера](https://core.telegram.org/bots/api#using-a-local-bot-api-server).
pyTelegramBotAPI также поддерживает эту функцию.
```python
from telebot import apihelper

apihelper.API_URL = "http://localhost:4200/bot{0}/{1}"
```
**Важно: как описано [здесь](https://core.telegram.org/bots/api#logout), вы должны выйти из своего бота с сервера Telegram перед переключением на локальный сервер API. В pyTelegramBotAPI используйте `bot.log_out()`**

*Примечание. 4200 - это пример порта.*

### Асинхронная отправка сообщений
Существует реализация TeleBot, которая выполняет все функции `send_xyz` и `get_me` асинхронно. Это __существенно__ может ускорить работу вашего бота , но у нее есть нежелательные побочные эффекты, если ее использовать без осторожности.
Чтобы включить это поведение, создайте экземпляр AsyncTeleBot вместо TeleBot.
```python
tb = telebot.AsyncTeleBot("TOKEN")
```
Теперь каждая функция, вызванная Telegram API, выполняется в отдельном потоке.  Функции изменены для возврата экземпляра AsyncTask (определенного в util.py).  Использование AsyncTeleBot позволяет делать следующее:
```python
import telebot

tb = telebot.AsyncTeleBot("TOKEN")
task = tb.get_me() # Выполнить вызов API
# Сделайте другие операции ...
a = 0
for a in range(100):
	a += 10

result = task.wait() # Получите результат 
```
*Примечание: если вы выполняете функции send_xyz друг за другом без вызова wait(), порядок доставки сообщений может быть неправильным.*

### Отправка больших текстовых сообщений
Иногда вам необходимо отправлять сообщения, длина которых превышает 5000 символов.  API Telegram не может обрабатывать такое количество символов в одном запросе, поэтому нам нужно разделить сообщение на несколько частей.  Вот как это сделать с помощью API:
```python
from telebot import util
large_text = open("large_text.txt", "rb").read()

# Разделите текст на каждые 3000 символов.
# split_string возвращает список с разделенным текстом.
splitted_text = util.split_string(large_text, 3000)

for text in splitted_text:
	tb.send_message(chat_id, text)
```

Или вы можете использовать новую функцию `smart_split`, чтобы получить более значимые подстроки:
```python
from telebot import util
large_text = open("large_text.txt", "rb").read()
# Разделяет одну строку на несколько строк с максимальным количеством `chars_per_string` (макс. 4096)
# Разбивается на последние '\n', '. ' или ' ' именно с таким приоритетом.
splitted_text = util.smart_split(large_text, chars_per_string=3000)
for text in splitted_text:
	tb.send_message(chat_id, text)
```
### Управление количеством потоков, используемых TeleBot
Конструктор TeleBot принимает следующие необязательные аргументы:

 - threaded: True/False (по умолчанию True). Флаг, указывающий
    TeleBot должен выполнять обработчики сообщений в своем потоке опроса.

### Механизм слушателя
В качестве альтернативы обработчикам сообщений можно также зарегистрировать функцию в качестве слушателя TeleBot.

ВНИМАНИЕ: обработчики не исчезнут!  Ваше сообщение будет обработано как обработчиками, так и слушателями.  Кроме того, из-за многопоточности невозможно предсказать, что сначала будет работать. Если вы используете threaded=False, кастомные слушатели сработают раньше, после них будут вызваны обработчики. 
Пример:
```python
def handle_messages(messages):
	for message in messages:
		# Сделайте что-нибудь с сообщением
		bot.reply_to(message, 'Привет')

bot.set_update_listener(handle_messages)
bot.polling()
```

### Использование веб-хуков
При использовании веб-перехватчиков telegram отправляет одно обновление на каждый вызов, для его обработки вы должны вызвать метод process_new_messages([update.message]), когда вы его получите.

Есть несколько примеров использования веб-перехватчиков в директории [examples/webhook_examples](examples/webhook_examples).

### Логирование

Вы можете использовать регистратор модуля Telebot для регистрации отладочной информации о Telebot. Используйте `telebot.logger`, чтобы получить логгер модуля TeleBot.
В регистратор можно добавить пользовательские обработчики журналов. Обратитесь к [странице модуля ведения журнала Python](https://docs.python.org/2/library/logging.html) для получения дополнительной информации.

```python
import logging

logger = telebot.logger
telebot.logger.setLevel(logging.DEBUG) # Выводит в консоль отладочные сообщения.
```

### Прокси

Вы можете использовать прокси для запросов. Объект `apihelper.proxy` будет использовать аргумент прокси при вызове `requests`.

```python
from telebot import apihelper

apihelper.proxy = {'http':'http://10.10.1.10:3128'}
```

Если вы хотите использовать прокси-сервер socket5, вам необходимо установить зависимость `pip install requests[socks]` и убедиться, что у вас установлена последняя версия `gunicorn`, ` PySocks`, `pyTelegramBotAPI`, ` requests` и `urllib3`.

```python
apihelper.proxy = {'https':'socks5://userproxy:password@proxy_address:port'}
```


## Соответствие API

_Идет проверка ..._

✅ [Bot API 4.5](https://core.telegram.org/bots/api-changelog#december-31-2019) _- Было проверенно_

* ✔ [Bot API 4.4](https://core.telegram.org/bots/api-changelog#july-29-2019)
* ✔ [Bot API 4.3](https://core.telegram.org/bots/api-changelog#may-31-2019)
* ✔ [Bot API 4.2](https://core.telegram.org/bots/api-changelog#april-14-2019)
* ➕ [Bot API 4.1](https://core.telegram.org/bots/api-changelog#august-27-2018) - Нет поддержки.
* ➕ [Bot API 4.0](https://core.telegram.org/bots/api-changelog#july-26-2018)   - Нет поддержки.
* ✔ [Bot API 3.6](https://core.telegram.org/bots/api-changelog#february-13-2018)
* ✔ [Bot API 3.5](https://core.telegram.org/bots/api-changelog#november-17-2017)
* ✔ [Bot API 3.4](https://core.telegram.org/bots/api-changelog#october-11-2017)
* ✔ [Bot API 3.3](https://core.telegram.org/bots/api-changelog#august-23-2017)
* ✔ [Bot API 3.2](https://core.telegram.org/bots/api-changelog#july-21-2017)
* ✔ [Bot API 3.1](https://core.telegram.org/bots/api-changelog#june-30-2017)
* ✔ [Bot API 3.0](https://core.telegram.org/bots/api-changelog#may-18-2017)
* ✔ [Bot API 2.3.1](https://core.telegram.org/bots/api-changelog#december-4-2016)
* ✔ [Bot API 2.3](https://core.telegram.org/bots/api-changelog#november-21-2016)
* ✔ [Bot API 2.2](https://core.telegram.org/bots/api-changelog#october-3-2016)
* ✔ [Bot API 2.1](https://core.telegram.org/bots/api-changelog#may-22-2016)
* ✔ [Bot API 2.0](https://core.telegram.org/bots/api-changelog#april-9-2016) 


## Журнал изменений

27.04.2020 - Poll и Dice обновлены. Соответствие Python2 больше не проверяется из-за EOL.

11.04.2020 - Рефакторинг. new_chat_member больше не поддерживается. Исправление в html_text. Началась проверка соответствия Bot API.

06.06.2019 - Добавлена ​​поддержка опросов (Poll). Добавлены функции send_poll, stop_poll

## F.A.Q.

### Bot 2.0

9 апреля 2016 года Telegram выпускает новое API бота 2.0, в которое внесена радикальная переработка специально для изменения интерфейса метода. Если вы хотите обновить API до последней версии, убедитесь, что вы переключили код бота на интерфейс метода бота 2.0.

[Дополнительная информация о поддержке pyTelegramBotAPI 2.0](https://github.com/eternnoir/pyTelegramBotAPI/issues/130)

### Как отличить пользователя от чата в message.chat?
Telegram Bot API поддерживает новый тип Chat для message.chat.

- Проверьте атрибут ```type``` в объекте ```Chat```:
-
```python
if message.chat.type == "private":
	# личное сообщение чата

if message.chat.type == "group":
	# сообщение группового чата

if message.chat.type == "supergroup":
	# сообщение в чате супергруппы

if message.chat.type == "channel":
	# сообщение канала

```

### Как я могу справиться с повторяющейся ошибкой ConnectionResetError?

Экземпляры ботов, которые долгое время простаивали, могут быть отклонены сервером при отправке сообщения из-за тайм-аута последнего использованного сеанса. Добавьте к вашей инициализации `apihelper.SESSION_TIME_TO_LIVE = 5 * 60`, чтобы принудительно восстановить сеанс через 5 минут без какой-либо активности.

## Чат-группа Telegram

Получить помощь. Обсуждение. Чат.

* Присоединяйся к [pyTelegramBotAPI чату](https://telegram.me/joinchat/Bn4ixj84FIZVkwhk2jag6A)
* Теперь у нас есть и Telegram-канал!  Будьте в курсе изменений API, и [присоединяйся к pyTelegramBotAPI каналу](https://telegram.me/pytelegrambotapi).

## Примеры ботов

* [Echo Bot](https://github.com/eternnoir/pyTelegramBotAPI/blob/master/examples/echo_bot.py)
* [Deep Linking](https://github.com/eternnoir/pyTelegramBotAPI/blob/master/examples/deep_linking.py)
* [next_step_handler Example](https://github.com/eternnoir/pyTelegramBotAPI/blob/master/examples/step_example.py)

## Боты, использующие этот API
* [SiteAlert bot](https://telegram.me/SiteAlert_bot) ([source](https://github.com/ilteoood/SiteAlert-Python)) by *ilteoood* - Следит за веб-сайтами и отправляет уведомление об изменениях
* [TelegramLoggingBot](https://github.com/aRandomStranger/TelegramLoggingBot) by *aRandomStranger*
* [Send to Kindle Bot](https://telegram.me/Send2KindleBot) by *GabrielRF* - Отправить в Kindle файлы или ссылки на файлы.
* [Telegram LMGTFY_bot](https://github.com/GabrielRF/telegram-lmgtfy_bot) ([source](https://github.com/GabrielRF/telegram-lmgtfy_bot)) by *GabrielRF* - Позвольте мне погуглить это для вас.
* [Telegram UrlProBot](https://github.com/GabrielRF/telegram-urlprobot) ([source](https://github.com/GabrielRF/telegram-urlprobot)) by *GabrielRF* - Сокращение URL-адресов и расширение URL-адресов.
* [Telegram Proxy Bot](https://github.com/mrgigabyte/proxybot) by *mrgigabyte* - `Ресурсы на оригинальную версию этого бота принадлежат` **Groosha** `,просто я добавил определенные функции, которые, как думал, были необходимы`.
* [RadRetroRobot](https://github.com/Tronikart/RadRetroRobot) by *Tronikart* - Многофункциональный Telegram-бот RadRetroRobot.
* [League of Legends bot](https://telegram.me/League_of_Legends_bot) ([source](https://github.com/i32ropie/lol)) by *i32ropie*
* [NeoBot](https://github.com/neoranger/NeoBot) by [@NeoRanger](https://github.com/neoranger)
* [TagAlertBot](https://github.com/pitasi/TagAlertBot) by *pitasi*
* [ColorCodeBot](https://t.me/colorcodebot) ([source](https://github.com/andydecleyre/colorcodebot)) - Делитесь фрагментами кода в виде красиво выделенного синтаксиса HTML и/или изображений.
* [ComedoresUGRbot](http://telegram.me/ComedoresUGRbot) ([source](https://github.com/alejandrocq/ComedoresUGRbot)) by [*alejandrocq*](https://github.com/alejandrocq) - Telegram-бот для просмотра меню столовой Университета Гранады.
* [picpingbot](https://web.telegram.org/#/im?p=%40picpingbot) - Забавный анонимный обмен фотографиями от Boogie Muffin.
* [TheZigZagProject](https://github.com/WebShark025/TheZigZagProject) - Бот 'All In One' для Telegram! by WebShark025
* [proxybot](https://github.com/p-hash/proxybot) - Простой прокси-бот для Telegram. by p-hash
* [DonantesMalagaBot](https://github.com/vfranch/DonantesMalagaBot)- DonantesMalagaBot предоставляет донорам крови в Малаге информацию о местах, где они могут сдавать кровь сегодня или в ближайшие дни.  Он также записывает дату последнего пожертвования, чтобы доноры знали, когда они могут снова сделать пожертвование.- by vfranch
* [DuttyBot](https://github.com/DmytryiStriletskyi/DuttyBot) by *Dmytryi Striletskyi* - Расписание для одного вуза в Киеве.
* [dailypepebot](https://telegram.me/dailypepebot) by [*Jaime*](https://github.com/jiwidi/Dailypepe) - Получит вам случайные изображения pepe и даст вам их идентификатор, затем вы можете назвать это изображение по номеру.
* [DailyQwertee](https://t.me/DailyQwertee) by [*Jaime*](https://github.com/jiwidi/DailyQwertee) - Бот, который управляет каналом,ежедневно отправляет футболки qwertee в 00:00.
* [wat-bridge](https://github.com/rmed/wat-bridge) by [*rmed*](https://github.com/rmed) - Отправляет и получает сообщения в/из WhatsApp через Telegram
* [flibusta_bot](https://github.com/Kurbezz/flibusta_bot) by [*Kurbezz*](https://github.com/Kurbezz)
* [EmaProject](https://github.com/halkliff/emaproject) by [*halkliff*](https://github.com/halkliff) - Ema - Eastern Media Assistant был создан для удобства использования. Кодирование здесь простое, но быстрое и мощное.
* [filmratingbot](http://t.me/filmratingbot)([source](https://github.com/jcolladosp/film-rating-bot)) by [*jcolladosp*](https://github.com/jcolladosp) - Telegram-бот, использующий Python API, который получает рейтинг фильмов от IMDb и metacritic
* [you2mp3bot](http://t.me/you2mp3bot)([link](https://storebot.me/bot/you2mp3bot)) - Этот бот может конвертировать видео Youtube в mp3.  Все, что вам нужно, это отправить URL-видео.
* [Send2Kindlebot](http://t.me/Send2KindleBot) ([source](https://github.com/GabrielRF/Send2KindleBot)) by *GabrielRF* - Отправить в сервис Kindle.
* [RastreioBot](http://t.me/RastreioBot) ([source](https://github.com/GabrielRF/RastreioBot)) by *GabrielRF* - Бот используется для отслеживания посылок в бразильской почтовой службе.
* [filex_bot](http://t.me/filex_bot)([link](https://github.com/victor141516/FileXbot-telegram))
* [Spbu4UBot](http://t.me/Spbu4UBot)([link](https://github.com/EeOneDown/spbu4u)) by *EeOneDown* - Бот с расписаниями для студентов СПбГУ.
* [SmartySBot](http://t.me/ZDU_bot)([link](https://github.com/0xVK/SmartySBot)) by *0xVK* - Бот-расписание для студентов Житомирского государственного университета имени Ивана Франко.
* [yandex_music_bot](http://t.me/yandex_music_bot)- Бесплатно скачивает треки / альбомы / публичные плейлисты из стримингового сервиса Яндекс.Музыка.
* [LearnIt](https://t.me/LearnItbot)([link](https://github.com/tiagonapoli/LearnIt)) - Telegram Bot создан, чтобы помочь людям запоминать словарный запас других языков.
* [MusicQuiz_bot](https://t.me/MusicQuiz_bot) by [Etoneja](https://github.com/Etoneja) - Прослушайте аудиозаписи и попробуйте назвать исполнителя песни.
* [Bot-Telegram-Shodan ](https://github.com/rubenleon/Bot-Telegram-Shodan) by [rubenleon](https://github.com/rubenleon)
* [MandangoBot](https://t.me/MandangoBot) by @Alvaricias - Бот для управления альянсами Marvel Strike Force (только на испанском, банкомат).
* [ManjaroBot](https://t.me/ManjaroBot) by [@NeoRanger](https://github.com/neoranger) - Бот для Manjaro Linux испанской группы с большим количеством информации для Manjaro Newbies.
* [VigoBusTelegramBot](https://t.me/vigobusbot) ([GitHub](https://github.com/Pythoneiro/VigoBus-TelegramBot)) - Бот, который показывает автобусы, прибывающие на определенную остановку, и их оставшееся время для города Виго (Галисия - Испания).
* [kaishnik-bot](https://t.me/kaishnik_bot) ([source](https://github.com/airatk/kaishnik-bot)) by *airatk* - бот, который показывает всю необходимую информацию студентам KNTRU-KAI.
* [Creation Date](https://t.me/creationdatebot) by @karipov - интерполирует даты создания учетной записи на основе telegram с заданным идентификатором
* [m0xbot](https://t.me/m0xbot) by [kor0p](https://github.com/kor0p) - крестики-нолики.  
* [kboardbot](https://t.me/kboardbot) by [kor0p](https://github.com/kor0p) - встроенные переключатели раскладки клавиатуры (English, Hebrew, Ukrainian, Russian).  
* [Robbie](https://t.me/romdeliverybot) ([source](https://github.com/FacuM/romdeliverybot_support)) by @FacuM - Поддержка бот для разработчиков и сопровождающих.
* [AsadovBot](https://t.me/asadov_bot) ([source](https://github.com/desexcile/BotApi)) by @DesExcile - Каталог стихов Эдуарда Асадова.
* [thesaurus_com_bot](https://t.me/thesaurus_com_bot) ([source](https://github.com/LeoSvalov/words-i-learn-bot)) by @LeoSvalov - слова и синонимы из [dictionary.com](https://www.dictionary.com) и [thesaurus.com](https://www.thesaurus.com) в telegram.
* [InfoBot](https://t.me/info2019_bot) ([source](https://github.com/irevenko/info-bot)) by @irevenko - Универсальный бот, отображающий некоторую статистику (погоду, время, криптовалюту и т.д.)
* [FoodBot](https://t.me/ChensonUz_bot) ([source](https://github.com/Fliego/old_restaurant_telegram_chatbot)) by @Fliego - простой бот для заказа еды
* [Sporty](https://t.me/SportydBot) ([source](https://github.com/0xnu/sporty)) by @0xnu - Telegram-бот для отображения последних новостей, расписания занятий спортом и обновлений травм.
* [Neural style transfer](https://t.me/ebanyivolshebnikBot) ([source](https://github.com/timbyxty/StyleTransfer-tgbot)) by @timbyxty - бот для переноса стиля с одной картинки на другую на основе нейросети.
* [JoinGroup Silencer Bot](https://t.me/joingroup_silencer_bot) ([source](https://github.com/zeph1997/Telegram-Group-Silencer-Bot)) by [@zeph1997](https://github.com/zeph1997) - Telegram Bot для удаления уведомлений о "присоединении к группе" и "удалении из группы".
* [AdviceBook](https://t.me/adviceokbot) by [@barbax7](https://github.com/barbax7) - Telegram Bot, который позволяет вам получать случайные советы по чтению, когда вы не знаете, какую книгу читать.
* [Blue_CC_Bot](https://t.me/Blue_CC_Bot) by [@Akash](https://github.com/BLUE-DEVIL1134) - Бот Telegram, который проверяет ваши кредитные карты и говорит, что это настоящая, а какая поддельная.
* [RandomInfoBot](https://t.me/RandomInfoBot) by [@Akash](https://github.com/BLUE-DEVIL1134) - Telegram-бот, который генерирует случайную информацию о людях, собранную с более чем 13 веб-сайтов.
* [TasksListsBot](https://t.me/TasksListsBot) ([source](https://github.com/Pablo-Davila/TasksListsBot)) by [@Pablo-Davila](https://github.com/Pablo-Davila) - В (задачах) перечислены боты-менеджеры для Telegram.
* [MyElizaPsychologistBot](https://t.me/TasksListsBot) ([source](https://github.com/Pablo-Davila/MyElizaPsychologistBot)) by [@Pablo-Davila](https://github.com/Pablo-Davila) - Реализация известного чат-бота психолога Элизы.
* [Evdembot](https://t.me/Evdembot) by Adem Kavak. Бот, который информирует вас обо всем, что вы хотите.
* [Frcstbot](https://t.me/frcstbot) ([source](https://github.com/Mrsqd/frcstbot_public)) by [Mrsqd](https://github.com/Mrsqd) - Telegram-бот, который всегда будет рад показать вам прогноз погоды.
* [Bot Hour](https://t.me/roadtocode_bot) маленький бот, который говорит время в разных странах by [@diegop384](https://github.com/diegop384) [repo](https://github.com/diegop384/telegrambothour)
* [moodforfood_bot](https://t.me/moodforfood_bot) Этот бот предоставит вам список закусочных рядом с вашим текущим местоположением в Telegram, которым вам будет предложено поделиться. API для всей этой информации взят с https://foursquare.com/. by [@sophiamarani](https://github.com/sophiamarani)
* [Donation with Amazon](https://t.me/donamazonbot) by [@barbax7](https://github.com/barbax7) Этот бот жертвует рекламные комиссии Amazon некоммерческой организации, выбранной пользователем.
* [COVID-19 Galicia Bot](https://t.me/covid19_galicia_bot) by [@dgarcoe](https://github.com/dgarcoe) Этот бот предоставляет ежедневные данные о кризисе COVID19 в Галисии (Испания), полученные из официальных правительственных источников.
* [MineGramBot](https://github.com/ModischFabrications/MineGramBot) by [ModischFabrications](https://github.com/ModischFabrications). Этот бот может запускать, останавливать и контролировать сервер minecraft.
* [Tabletop DiceBot](https://github.com/dexpiper/tabletopdicebot) by [dexpiper](https://github.com/dexpiper). Этот бот может бросать несколько кубиков для ролевых игр, добавлять положительные и отрицательные модификаторы и показывать короткие описания бросков.
* [BarnameKon](https://t.me/BarnameKonBot) by [Anvaari](https://github.com/anvaari). Этот бот делает ссылку «Добавить в календарь Google» для ваших мероприятий.  Он дает информацию о событии и ссылку для возврата.  Он работает для календаря Джалали и по тегеранскому времени. [Source code](https://github.com/anvaari/BarnameKon)
* [Price Tracker](https://t.me/trackokbot) by [@barbax7](https://github.com/barbax7). Этот бот отслеживает цены на продукты amazon.it, которые интересуют пользователя, и уведомляет его, когда одна цена снижается.
* [Torrent Hunt](https://t.me/torrenthuntbot) by [@Hemantapkh](https://github.com/hemantapkh/torrenthunt). Бот Torrent Hunt - это многоязычный бот с поддержкой встроенного режима для поиска и изучения торрентов с сайта 1337x.to.
* Translator bot by [Areeg Fahad (source)](https://github.com/AREEG94FAHAD/translate_text_bot). Этого бота можно использовать для перевода текстов.
* Digital Cryptocurrency bot by [Areeg Fahad (source)](https://github.com/AREEG94FAHAD/currencies_bot). С помощью этого бота вы теперь можете отслеживать цены более чем 12 цифровых криптовалют.
* [Anti-Tracking Bot](https://t.me/AntiTrackingBot) by [Leon Heess (source)](https://github.com/leonheess/AntiTrackingBot). Отправьте любую ссылку, и бот изо всех сил попытается удалить все отслеживание с отправленной вами ссылки.
* [Developer Bot](https://t.me/IndDeveloper_bot) by [Vishal Singh](https://github.com/vishal2376) [(source code)](https://github.com/vishal2376/telegram-bot) Этот бот для телеграмм может выполнять такие задачи, как поиск и клонирование GitHub, предоставлять ресурсы для обучения C ++, поиск в Stackoverflow, Codeforces (визуализатор профиля, случайные проблемы).
* [oneIPO bot](https://github.com/aaditya2200/IPO-proj) by [Aadithya](https://github.com/aaditya2200) & [Amol Soans](https://github.com/AmolDerickSoans) Этот бот Telegram предоставляет живые обновления, данные и документы о текущих и предстоящих IPO (первичных публичных предложениях). 
	
**Обнаружили ошибки или неверный перевод, пишите в TG - [@grystny_chel](https://t.me/grystny_chel)**
