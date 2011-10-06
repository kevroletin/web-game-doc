**SMALLWORLD** - ALPHA EDITION
Version 0.001
Readme File
September 15, 2011


Предисловие:
===================

Спецификация протокола игры SMALLWORLD.

Содержание
=================

1. Введение
1. Незадокументированные особенности
1. Известные проблемы
1. Описание протокола
1. Контакты разработчиков


I. Введение
===============
SMALLWORLD - приложение, основанное на модели клиент-сервер с
использованием JSON.

II. Незадокументированные особенности
=========================


III. Известные проблемы
=================

V.  Описание протокола
========================
Сервер получает **JSON** объёкт в теле **http POST** запроса. Формат
**JSON** объекта в запросе и допустимые ответы сервера описаны ниже

Тестовый запрос
----------
Для тестирование допустакается следующее необязательное расширение
протокола:
тестовый запрос это JSON объект, содержащий 2 поля: "test" и
"description"(опциональное). Поле "test" - список JSON объектов, каждый из которых является валидной
командой серверу. Формат и доступные команды специфицированы в этом
докумете и приложениях.

Типичный ответ
-----------------
**Response** объект JSON следующего формата (в случае тестового
запроса список следующих объектов):

    {
      'result': <ans>,
      other fields with parameters 
    },

<**ans**> может имеет одно из следующих значений(или другие значения -
в зависимости от переданного запроса):

* `ok`: Операция завершена успешно. 
* `badAction`: Поле `action` содержит недопустимое значение.
* `badJson`: Невалидный JSON в запросе, либо в перереданном объекте
отсутствут все поля, необходимые для переданного в запросе action.
* `badUsername`: Переданно недопустимое имя пользователя.
* `badPassword`: Передан недопустимый пароль.
* `usernameTaken`: Пользователь с заданным именем уже зарегестрирован.
* `badUsernameOrPassword`: Неверное имя пользователя или пароль.
* `badSid`: Клиент передал невалидный идентефикатор сессии.


Базовые команды
--------------
  

###resetServer
#####Format:
    {
      "action": "resetServer"
    }
#####Success:
    {
      "result" "ok"
    }
#####Fail:
    Not defined.
#####Description:
Очищает базу данные сервера. Необходимо для тестирования.
  
###Register
#####Format:
    {
      "action" : "register", 
      "username" : "<name>",
      "password" : "<password>"
    }

<**name**> - строка состоящая из 3-16 символов, начиная с латинской
буквы. Может содержать латинские буквы, цифры, символ нижнего подчерка '_'
и дефис '-'.
<**password**> - строка из 6-18 произвольных символов ASCII.
    
#####Success:
    {'result': 'ok'}
    
#####Fail:
    {'result': 'badPassword' },
    {'result': 'badUsername' },
    {'result': 'badJson' },
    {'result': 'usernameTaken' },
    
#####Description:
Регистрация пользователя.

###Login
#####Format:
    {
      "action" : "login", 
      "username" : "<name>",
      "password" : "<password>"
    }
    
#####Success:
    {'result': 'ok', 'sid': <sid>}
    
#####Fail:
    {'result': 'badJson'},
    {'result': 'badUsernameOrPassword'}
    
#####Description:
Аутентефикая пользователя. Сервер возвращает идентефикатор сессии
**sid**, необходимый для совершения последующий действий
пользоваетлем.
Для тестирования sid-ы это целые числа, начиная с 1цы.

###Logout
#####Format:
    {
      "action" : "logout", 
      "sid" : <sid>,
    }

#####Success:
    {'result': 'ok'}
    
#####Fail:
    {'result': 'badJson'},
    {'result': 'badSid'}
    
      
###doSmth
#####Format:
    {
      "action" : "doSmtn", 
      "sid" : <sid>,
    }
#####Success:
      {'result': 'ok'}
    
#####Fail:
    {'result': 'badJson'},
    {'result': 'badSid'}
    
#####Description:
Для тестирования. Команда, которая не делает ничего.
  
###uploadMap
#####Format:
    {
      "action": "uploadMap",
      "mapName": "<mapName>",
      "playersNum": <playersNum>
      "regions": <regions>
    }


#####Success:
      {"result": "ok", "mapId": <mapId>}
    
#####Fail:
    {"result": "badJson"},
    {"result": "badMapName"},
    {"result": "badPlayersNum"}
      
#####Description:
Создаёт новую карту.
**name** - уникальное имя карты - строка из 1-16 символов.
**playersNum** - максимальное количество игроков на карте. Целое число
из интервала [2, 5]
**<regions>** - TODO:
      
###createGame
#####Format:
    {
      "action": "createGame",
      "sid": <sid>,
      "gameName": "<gameName>",
      "mapId": <mapId>,
      "playersNum": <playersNum>,
      "gameDescr": "<gameDescription>" //optional
    }
#####Success:
      {"result": "ok", "gameId": <gameId>}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badSid"},
      {"result": "badMap"},
      {"result": "badNumberOfPlayers"},
      {"result": "badGameName"},
      {"result": "badGameDescription"}
      
#####Description:
Создание новой игры.

**gameName** - строка из 1-50 символов. Иначе возарвщается `{"result": "badGameName"}`. 

**mapId** - id карты, имеющейся на сервере. Иначе возарвщается `{"result": "badMap"}`

**playersNum** - целое число, которые  **меньше либо равно** playerNum
  выбранной карты, иначе возвращается `{"result": "badnumberOfPPlayers"}`)

**gameDescription** необязательное поле. Строка не длинее 300
  символов. Иначе `{"result": "badGameDescription"}`

######sendMessage
    TODO:

######getMessages
    TODO:

######createDefaultMaps
    TODO:

######uploadMap
    TODO:

######createGame
    TODO:

######getGameList
    TODO:

######joinGame
    TODO:
    
######leaveGame
    TODO:

######setReadinessStatus
    TODO:

######selectRace
    TODO:

######conquer
    TODO:

######decline
    TODO:

######finishTurn
    TODO:

######redeploy
    TODO:

######defend
    TODO:

######enchant
    TODO:

#####getVisibleTokenBadges
    TODO:

####throwDice
    TODO:

