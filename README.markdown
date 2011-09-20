**SMALLWORLDS** - ALPHA EDITION
Version 0.001
Readme File
September 15, 2011


About This Document:
===================

This README file includes information that pertains to general
problems and questions you may have concerning the program or your
computer. 
Should you experience any problems with SMALLWORLDS - ALPHA EDITION, please refer to this file 
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
SMALLWORLDS is JSON-based client-server application. Details are yet to be revealed.

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
"test" field is list or just one JSON object, that describes the query.
All possible types of queries and its formats are described in section BASIC COMMANDS

TYPICAL RESPONSES
-----------------
**Response** is a list of JSON objects created in following format:

    {
      'result': <ans>,
      other fileds with parameters 
    },

<**ans**> can possess one of the following values:

* ok: String or list containing this word in lower case usually means that operation went succesfully. 
* badAction: Action field from JSON query is unknown for server
* badJson: Incorrent format of JSON message were received. Read the BASIC COMMANDS subsection below for more information on the subject.
* badUsername: Entered username is not allowed. Check out "registration" command format in BASIC COMMANDS subsection.
* badPassword: Entered password is not allowed. Same advice as before.
* usernameTaken: Pretty much self-explanatory. Think up a new one.
* badUsernameOrPassword: Either user with this username hasn't signed up yet or password differs from one entered during registration.
* userLoggedIn: User were trying to log in without loggin' out first. Regrettably, it's not possible.
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
      
<**password**> is a 6-18 characters string starting from latin letter
or number. It may omly contain latin letters and numbers.
    
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
Do something. Just for testing. Returns 'badJson' if the JSON object
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
Creates new map with mapName name, name must be UNIQUE string with
length less than 16 symbols, otherwise returns `{"result":
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
      {"result": "badMap"},
      {"result": "badNumberOfPlayers"},
      {"result": "badGameName"},
      {"result": "badGameDescription"}
      
#####Description:
Creates new game. 

**Sid** must be a valid session id of one of users who doesn't play in
  other game, otherwise returns `{"result": "badSid"}`

**gameName** must be **UNIQUE** string that length is in interval [1,
  50], otherwise function returns `{"result": "badGameName"}`. 

**mapId** must be valid id of map, otherwse returns {"result":
  "badMap"}

**playersNum** is an integer that is equal to playerNum of chosen
  map(otherwise `{"result": "badnumberOfPPlayers"}`)

**gameDescription** is an optional field, that length must be less
  than 300(`{"result": "badGameDescription"}` otherwise)
