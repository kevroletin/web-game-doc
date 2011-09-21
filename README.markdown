**SMALLWORLD** - ALPHA EDITION
Version 0.001
Readme File
September 15, 2011


About This Document:
===================

This README file includes information that pertains to general
problems and questions you may have concerning the program or your
computer. 
Should you experience any problems with SMALLWORLD - ALPHA EDITION, please refer to this file 
for additional help on answering questions about the program and solving technical difficulties.

TABLE OF CONTENTS
=================

1. INTRODUCTION
1. UNDOCUMENTED FEATURES
1. SYSTEM REQUIREMENTS
1. KNOWN ISSUES
1. PROTOCOL DESCRIPTION
1. CONTACTING DEVELOPERS   


I. INTRODUCTION
===============
SMALLWORLD is JSON-based client-server application. Details are yet to be revealed.

II. UNDOCUMENTED FEATURES
=========================
Will have to add them later.

III.  SYSTEM REQUIREMENTS
=========================
**Someone fill this section for me.**

IV.  KNOWN ISSUES
=================
Everything works just fine.

V.  PROTOCOL DESCRIPTION
========================

Server receives **JSON** messages sent via **http POST** in request
**body**. Typical responses are described below under TYPICAL
RESPONSES title.


TEST QUERY
----------
Each query must be JSON object with "test" and "description"(optional) field.
"test" field is list or just one JSON object that describes the query.
All possible types of queries and its formats are described in section BASIC COMMANDS

TYPICAL RESPONSES
-----------------
**Response** is a list of JSON objects created in following format:

    {
      'result': <ans>,
      other fields with parameters 
    },

<**ans**> can possess one of the following values:

* ok: String or list containing this word in lower case usually means that operation went succesfully. 
* badAction: Action field from JSON query is unknown for server
* badJson: Incorrent format of JSON message were received. Read the BASIC COMMANDS subsection below for more information on the subject.
* badUsername: Entered username is not allowed. Check out "registration" command format in BASIC COMMANDS subsection.
* badPassword: Entered password is not allowed. Same advice as before.
* usernameTaken: Pretty much self-explanatory. Think up a new one.
* badUsernameOrPassword: Either user with this username hasn't signed up yet or password differs from one entered during registration.
* userLoggedIn: User was trying to log in without loggin' out first. Regrettably, it's not possible yet.
* badSid: User was trying to do something with incorrect session id. Read "Logout" command description to understand what kind of sid you can use.

For "login" query the second field is `"sid": sid`

BASIC COMMANDS
--------------
  
NOTE: Each command must be a list, that contains JSON objects. Each
such JSON message should contain "action" field. Otherwise, 'badJson'
error's to be expected.
  
###Register
#####Format:
    {
      "action" : "register", 
      "username" : "<name>",
      "password" : "<password>"
    }

<**name**> is a 3-16 characters string starting from latin letter. It
may contain latin letters, numbers, underlines, hyphens and nothing
else
      
<**password**> is a 6-18 characters string. It may contain any ASCII
character you like 
    
#####Success:
    {'result': 'ok'}
    
#####Fail:
    {'result': 'badPassword' },
    {'result': 'badUsername' },
    {'result': 'badJson' },
    {'result': 'usernameTaken' },
    
#####Description:
Adds the user to the database so he can log in later. It's the only
command unregistered user can execute.


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
Logs the user in. **Returns a pair containing the session ID**. For
tests sids are generated as sequence of integers. User can't do
anything (but register)  without being logged in first.
 

###Logout
#####Format:
    {
      "action" : "logout", 
      "sid" : <sid>,
    }

<**sid**> is a number received from server when "login" command is
executed. Anything else is not a <**sid**>.

      
#####Success:
    {'result': 'ok'}
    
#####Fail:
    {'result': 'badJson'},
    {'result': 'badSid'}
    
#####Description:
Logs the user out. Logged out user can't use his sid anymore.
      
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
Does something. Just for testing. Returns 'badJson' if the JSON object
is incorrect, 'badSid' if there is no logged in user with the
specified sid.

  
###uploadMap
#####Format:
    {
      "action": "uploadMap",
      "mapName": "<mapName>",
      "playersNum": <playersNum>
    }
#####Success:
      {"result": "ok", "mapId": <mapId>}
    
#####Fail:
    {"result": "badJson"},
    {"result": "badMapName"},
    {"result": "badPlayersNum"}
      
#####Description:
Creates a new map with mapName name, name must be UNIQUE string 
with length less than 16 symbols, otherwise returns `{"result":
"badMapName"}`. 

      The playersNum must be an integer in interval:[2, 5].
      
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
      {"result": "badMapId"},
      {"result": "badNumberOfPlayers"},
      {"result": "badGameName"},
      {"result": "badGameDescription"}
      
#####Description:
Creates new game. 

**Sid** must be a valid session id of one of users who doesn't play in
  other game, otherwise returns `{"result": "badSid"}`

**gameName** must be **UNIQUE** string whose length is in interval [1,
  50], otherwise function returns `{"result": "badGameName"}`. 

**mapId** must be valid id of map, otherwise returns {"result":
  "badMapId"}

**playersNum** is an integer that is equal to playerNum of chosen
  map(otherwise `{"result": "badnumberOfPPlayers"}`)

**gameDescription** is an optional field, whoset length must be less
  than 300(`{"result": "badGameDescription"}` otherwise)

###uploadMap
#####Format:
    {
      "action": "uploadMap",
      "mapName": "<mapName>",
      "playersNum": <playersNum>,
    }
#####Success:
      {"result": "ok", "mapId": <mapId>}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badMapName"},
      {"result": "badPlayersNum"}
      
#####Description:
Upload map with specified name and number of players

**mapName** must be **UNIQUE** string whose length is in interval [1,
  15], otherwise function returns `{"result": "badMapName"}`. 

**playersNum** is an integer in interval[1, 15](otherwise `{"result": "badPlayersNum"}`)
  
###createDefaultMaps
#####Format:
    {
      "action": "createDefaultMaps"
	}
#####Success:
      {"result": "ok"}
      
#####Fail:
      The same as in uploadMap
      
#####Description:
Create 4 default maps: defaultMap1(playersNum = 2), defaultMap2(playersNum = 3), defaultMap3(playersNum = 4), defaultMap4(playersNum = 5)

###joinGame
#####Format:
    {
      "action": "joinGame",
      "sid": <sid>,
      "gameId": <gameName>
    }
#####Success:
      {"result": "ok"}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badSid"},
      {"result": "badGameId"},
	  {"result": "badGameState"},
	  {"result": "alreadyInGame"},
	  {"result": "tooManyPlayers"}
      
#####Description:
User with specified sid joins the game with id = gameId

**Sid** must be a valid session id of one of users who doesn't play in
  other game, otherwise returns `{"result": "badSid"}`

**gameId** must be valid id of game, otherwise returns {"result":
  "badGameId"}
If game status is not equal to 'waiting the begining', function returns {"result": "badGameState"}.
If the user with specified session id is playing or waiting the begining of another game, it returns {"result": "alreadyInGame"}.
If there are no free space on map for new users, it returns {"result": "tooManyPlayers"}

###leaveGame
#####Format:
    {
      "action": "leaveGame",
      "sid": <sid>
    }
#####Success:
      {"result": "ok"}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badSid"},
      {"result": "notInGame"}
      
#####Description:
User with specified sid leaves the game
If user doesn't play in any game, it returns {"result": "notInGame"}

**Sid** must be a valid session id of one of users who plays in any game, otherwise returns `{"result": "badSid"}`

###setReadinessStatus
#####Format:
    {
      "action": "setReadinessStatus",
      "sid": <sid>,
	  "readinessStatus": <status>
    }
#####Success:
      {"result": "ok"}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badSid"},
      {"result": "notInGame"},
	  {"result": "badGameState"},
      {"result": "badReadinessStatus"}
      
#####Description:
Changes user's readiness status. If new status == 1 and it's the last ready user, the game transers to the state 'processing'.
If user doesn't play in any game, it returns {"result": "notInGame"}.
User cannot change his readiness status if the game isn't in state 'waiting'(in other game states it returns {"result": "badGameState"})

**Sid** must be a valid session id of one of users who plays in game, otherwise it returns`{"result": "badSid"}`

**status** may be equal to 0 if user isn't ready, and 1 if he/she is ready to play? otherwise function returns `{"result": "badReadinessStatus"}`  

###getMessages
#####Format:
    {
      "action": "getMessages",
      "since": <since>
    }
#####Success:
       {"result": "ok", "mesArray": mesArray}
      
#####Fail:
      {"result": "badJson"}
      
#####Description:
Get 100 last messages from <since> time

**since** must be a positive float value, otherwise function returns `{"result": "badSid"}`

**mesArray** is a list of objects such as {"userId": userId, "message": message, "mesTime": mesTime}

###sendMessage
#####Format:
    {
      "action": "sendMessage",
      "userId": <userId>,
	  "message": "<message>"
    }
#####Success:
      {"result": "ok", , "mesTime": mesTime}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badUserId"}
      
#####Description:
Send message with "<message>" text from user with id = <userId>.

**userId** must be a valid user id of one of users, otherwise it returns `{"result": "badUserId"}`

**message** is a text that must be sent

**mesTime** is a time of sending