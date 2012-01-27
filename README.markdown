**SMALLWORLD** - ALPHA EDITION
Version 0.001
Readme File
October 17, 2011


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
1. RACE DESCRIPTION   


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

COMMANDS LIST
-------------

* Register
* Login
* Logout
* uploadMap
* createGame
* createDefaultMaps
* joinGame
* aiJoin
* leaveGame
* setReadinessStatus
* getMessages
* sendMessage
* selectRace
* resetServer
* conquer
* decline
* redeploy
* selectFriend
* finishTurn
* defend
* dragonAttack
* enchant
* throwDice
* getGameList
* getGameState
* getMapList
* saveGame
* loadGame

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
* badUserSid: User was trying to do something with incorrect session id. Read "Logout" command description to understand what kind of sid you can use.

For "login" query the second field is `"sid": sid`

BASIC COMMANDS
--------------
  
NOTE: Each command must be a list, that contains JSON objects. Each
such JSON message should contain "action" field. Otherwise, 'badJson'
error's to be expected.

###resetServer
#####Format:
    {
      "action": "resetServer",
      "sid": <sid> //optional
    }
#####Success:
       {"result": "ok"}
      
#####Fail:
    {"result": "badJson"}

      
#####Description:
Returns server to the initial state(clears all tables in database)
  
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
    {'result': 'ok', 'sid': <sid>, 'userId': <userId>}
    
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
    {'result': 'badUserSid'}
    
#####Description:
Logs the user out. Logged out user can't use his sid anymore.
      
###uploadMap
#####Format:
    {
      "action": "uploadMap",
      "mapName": "<mapName>",
      "playersNum": <playersNum>,
      "turnsNum": <turnsNum>,
      "regions": [{"population": <population>, "landDescription": [<landDescription>], "adjacent": [<adjacentRegion>]}]
    }
#####Success:
    {"result": "ok", "mapId": <mapId>}
      
#####Fail:
    {"result": "badJson"},
    {"result": "mapNameTaken"},
    {"result": "badMapName"},
    {"result": "badPlayersNum"},
    {"result": "badTurnsNum"}
    {"result": "badRegions"}
      
#####Description:
Creates a new map with mapName name, name must be UNIQUE string 
with length less than 16 symbols, otherwise returns either
`{"result": "mapNameTaken"}` or
`{"result": "badMapName"}`. 

**playersNum** is an integer in interval[2, 5](otherwise `{"result": "badPlayersNum"}`)
  
**turnsNum** is an integer in interval[5, 10](otherwise `{"result": "badTurnsNum"}`) 
  
**regions** is a list of regions description. All regions in list are numerated in ascending order, starting from 1.
Each description **must** include list of **adjacent regions**(number of regions), 
and may include **population** -- initial number of tokens of lost tribes and  
list of **landDescription**, that can be evaluate to one of the following descriptions:
    [
        'border',
        'coast',
        'sea',
        'mountain',
        'mine',
        'farmland',
        'magic',
        'forest',
        'hill',
        'swamp',
        'cavern'
    ], otherwise returns `{"result": "badRegions"}`
If adjacent regions links aren't double-sided or sum of regions population more then 
maximum number of lost tribes tokens, also returns `{"result": "badRegions"}`
      
###createGame
#####Format:
    {
      "action": "createGame",
      "sid": <sid>,
      "gameName": "<gameName>",
      "mapId": <mapId>,
      "gameDescr": "<gameDescription>", //optional
	  "ai": <aiQuantity> //optional
    }
#####Success:
    {"result": "ok", "gameId": <gameId>}
      
#####Fail:
    {"result": "badJson"},
    {"result": "badUserSid"},
    {"result": "badMapId"}
    {"result": "badGameName"},
    {"result": "gameNameTaken"},
    {"result": "badGameDescription"},
    {"result": "alreadyInGame"},
	{"result": "tooManyPlayersForMap"}
      
#####Description:
Creates new game.

**Sid** must be a valid session id of one of users,
  otherwise returns `{"result": "badUserSid"}`

If the user with specified session id playing or waiting the begining
of another game, it returns `{"result": "alreadyInGame"}`.

**gameName** must be **UNIQUE** string whose length is in interval [1,
  50], otherwise function returns either {"result": "gameNameTaken"} or
  `{"result": "badGameName"}`.

**mapId** must be valid id of map, otherwise returns {"result":
  "badMapId"}

**playersNum** is an integer that is equal to playerNum of chosen
  map(otherwise `{"result": "badnumberOfPPlayers"}`)

**gameDescription** is an optional field, whoset length must be less
  than 300(`{"result": "badGameDescription"}` otherwise)
  
**ai** is an optional field, an integer describing AI quantity in game
  if ai is greater then playerNum of chosen map the result will be
  `{"result": "tooManyPlayersForMap"}`

**regions** is list of region indexes for chosen map

  
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
Create 7 default maps:

[
	{'mapName': 'defaultMap1', 'playersNum': 2, 'turnsNum': 5},
	{'mapName': 'defaultMap2', 'playersNum': 3, 'turnsNum': 5},
	{'mapName': 'defaultMap3', 'playersNum': 4, 'turnsNum': 5},
	{'mapName': 'defaultMap4', 'playersNum': 5, 'turnsNum': 5},
	{
		'mapName': 'defaultMap5', 
		'playersNum': 2, 
		'turnsNum': 5,
	 	'regions' : 
	 	[
	 		{
	 			'population' : 1,
	 			'landDescription' : ['mountain'],
	 			'adjacent' : [3, 4] 
	 		},
	 		{
	 			'population' : 1,
	 			'landDescription' : ['sea'],
	 			'adjacent' : [1, 4] 
	 		},
	 		{
	 			'population' : 1,
	 			'landDescription' : ['border', 'mountain'],
	 			'adjacent' : [1] 
	 		},
	 		{
	 			'population' : 1,
	 			'landDescription' : ['coast'],
	 			'adjacent' : [1, 2] 
	 		}
	 	]
	},
	{
		'mapName': 'defaultMap6', 
		'playersNum': 2, 
		'turnsNum': 7,
	 	'regions' : 
	 	[
	 		{
	 			'landDescription' : ['sea', 'border'], #1
	 			'adjacent' : [2, 17, 18] 
	 		},
	 		{
	 			'landDescription' : ['mine', 'border', 'coast', 'forest'], #2
	 			'adjacent' : [1, 18, 19, 3] 
	 		},
	 		{
	 			'landDescription' : ['border', 'mountain'], #3
	 			'adjacent' : [2, 19, 21, 4] 
	 		},
	 		{
	 			'landDescription' : ['farmland', 'border'], #4
	 			'adjacent' : [3, 21, 22, 5] 
	 		},
	 		{
	 			'landDescription' : ['cavern', 'border', 'swamp'], #5
	 			'adjacent' : [4, 22, 23, 6] 
	 		},
			{
				'population': 1,
	 			'landDescription' : ['forest', 'border'], #6
	 			'adjacent' : [5, 23, 7] 
	 		},
			{
	 			'landDescription' : ['mine', 'border', 'swamp'], #7
	 			'adjacent' : [6, 23, 8, 24, 26] 
	 		},
	 		{
	 			'landDescription' : ['border', 'mountain', 'coast'], #8
	 			'adjacent' : [7, 26, 10, 9, 24] 
	 		},
	 		{
	 			'landDescription' : ['border', 'sea'], #9
	 			'adjacent' : [8, 10, 11] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['cavern', 'coast'], #10
	 			'adjacent' : [9, 8, 11, 26] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['mine', 'coast', 'forest', 'border'], #11
	 			'adjacent' : [10, 26, 27, 12] 
	 		},
	 		{
	 			'landDescription' : ['forest', 'border'], #12
	 			'adjacent' : [11, 27, 30, 13] 
	 		},
	 		{
	 			'landDescription' : ['mountain', 'border'], #13
	 			'adjacent' : [12, 30, 28, 14] 
	 		},
	 		{
	 			'landDescription' : ['mountain', 'border'], #14
	 			'adjacent' : [13, 28, 16, 15] 
	 		},
	 		{
	 			'landDescription' : ['hill', 'border'], #15
	 			'adjacent' : [14, 16] 
	 		},
	 		{
	 			'landDescription' : ['farmland', 'magic', 'border'], #16
	 			'adjacent' : [15, 20, 28, 17] 
	 		},
	 		{
	 			'landDescription' : ['border', 'mountain', 'cavern', 'mine', #17 
	 				'coast'],
	 			'adjacent' : [16, 20, 1, 18] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['farmland', 'magic', 'coast'], #18
	 			'adjacent' : [17, 20, 1, 19] 
	 		},
	 		{
	 			'landDescription' : ['swamp'], #19
	 			'adjacent' : [18, 3, 21, 2, 20] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['swamp'], #20
	 			'adjacent' : [19, 28, 29, 21] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['hill', 'magic'], #21
	 			'adjacent' : [20, 29, 3, 4, 22] 
	 		},
	 		{
	 			'landDescription' : ['mountain', 'mine'], #22
	 			'adjacent' : [21, 25, 29, 4, 5, 23] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['farmland'], #23
	 			'adjacent' : [22, 25, 6, 5, 24] 
	 		},
	 		{
	 			'landDescription' : ['hill', 'magic'], #24
	 			'adjacent' : [23, 26, 7, 25, 8] 
	 		},
	 		{
	 			'landDescription' : ['mountain', 'cavern'], #25
	 			'adjacent' : [24, 22, 23, 26] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['farmland'], #26
	 			'adjacent' : [25, 24, 7, 8, 10, 11, 27] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['swamp', 'magic'], #27
	 			'adjacent' : [26, 11, 12, 30, 29] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['forest', 'cavern'], #28
	 			'adjacent' : [29, 30, 13, 14, 16, 20] 
	 		},
	 		{
	 			'landDescription' : ['sea'],
	 			'adjacent' : [28, 20, 21, 22, 25, 27, 30]  #29
	 		},
	 		{
	 			'landDescription' : ['hill'],  #30
	 			'adjacent' : [29, 28, 13, 12, 27] 
	 		},
	 	]
	},	{
		'mapName': 'defaultMap7', 
		'playersNum': 2, 
		'turnsNum': 5,
	 	'regions' : 
	 	[
	 		{
	 			'landDescription' : ['border', 'mountain', 'mine', 'farmland','magic'],
	 			'adjacent' : [2] 
	 		},
	 		{
	 			'landDescription' : ['mountain'],
	 			'adjacent' : [1, 3] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['mountain', 'mine'],
	 			'adjacent' : [2, 4] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['mountain'],
	 			'adjacent' : [3, 5] 
	 		},
			{
	 			'landDescription' : ['mountain', 'mine'],
	 			'adjacent' : [4] 
	 		}
	 	]
	}	
			
] 
        

###joinGame
#####Format:
    {
      "action": "joinGame",
      "sid": <sid>,
      "gameId": <gameId>
    }
#####Success:
      {"result": "ok"}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badUserSid"},
      {"result": "badGameId"},
      {"result": "badGameState"},
      {"result": "alreadyInGame"},
      {"result": "tooManyPlayers"}
      
#####Description:
User with specified sid joins the game with id = gameId

**Sid** must be a valid session id of one of the users,
  otherwise the function returns `{"result": "badUserSid"}`

**gameId** must be a valid id of game, otherwise returns `{"result": "badGameId"}`
If game status isn't 'waiting', function returns `{"result": "badGameState"}`.
If the user with specified session id playing or waiting the begining
of another game, it returns `{"result": "alreadyInGame"}`.

If there are no free space on map for new users, it returns 
`{"result": "tooManyPlayers"}`

###aiJoin
#####Format:
    {
      "action": "aiJoin",
      "gameId": <gameId>
    }
#####Success:
      {"result": "ok"}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badGameId"},
      {"result": "badGameState"},
      {"result": "tooManyPlayers"}
      
#####Description:
AI joins the game with id = gameId

**gameId** must be a valid id of game, otherwise returns `{"result": "badGameId"}`
If game status isn't 'waiting', function returns `{"result": "badGameState"}`.

If there are no free space on map for new users, it returns 
`{"result": "tooManyPlayers"}`

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
      {"result": "badUserSid"},
      {"result": "notInGame"}
      
#####Description:
User with specified sid leaves the game
If user aren't playing any game, it returns `{"result": "notInGame"}`

**Sid** must be a valid session id of one of the users playing some
  game, otherwise returns `{"result": "badUserSid"}`


###setReadinessStatus
#####Format:
    {
      "action": "setReadinessStatus",
      "sid": <sid>,
      "isReady": <status>,
      "visibleRaces": [<visibleRace>], 
      "visibleSpecialPower": [<visibleSpecialPower>]
    }
#####Success:
      {"result": "ok"}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badUserSid"},
      {"result": "notInGame"},
      {"result": "badGameState"},
      {"result": "badReadinessStatus"}
      
#####Description:
Changes user's readiness status. If new status == 1 and it's the last
ready user, the game transers to the state 'processing'.

If user aren't playing any game, it returns `{"result": "notInGame"}`.

User cannot change his readiness status if the game isn't in state
'waiting'(in other game states it returns `{"result": "badGameState"}`)


**Sid** must be a valid session id of one of users who plays in game,
otherwise it returns`{"result": "badUserSid"}`


**status** may be equal to 0 if the user isn't ready, and 1 if he/she
is ready to play, otherwise function returns 
`{"result": "badReadinessStatus"}`  


**visibleRaces** and **visibleSpecialPower** are optional settings
used only for testing. They must include the list of names of 6
races and specialPowers. Names must be correct. This commands allows
determine what races will be on the desk at the beginning of the
game.

###getMessages
#####Format:
    {
      "action": "getMessages",
      "since": <since>
    }
#####Success:
    {"result": "ok", "messages": messages}
      
#####Fail:
    {"result": "badJson"}
      
#####Description:
Get 100 last messages with ids greater then <since>

**since** must be a not negative integer value, otherwise function returns
`{"result": "badSince"}`

**messages** is a list of objects such as {"id": id,
"text": text, "time": time, "userId": userId}

In the test mode time of message is equal its id

###sendMessage
#####Format:
    {
      "action": "sendMessage",
      "sid": <sid>,
      "text": "<text>"
    }
#####Success:
    {"result": "ok"}
      
#####Fail:
    {"result": "badJson"},
    {"result": "badUserSid"},
    {"result": "badMessageText"}
      
#####Description:
Sends a message with <**text**> text from user with sid = <**Sid**>.

**Sid** must be a valid session id of one of users,
  otherwise it returns`{"result": "badUserSid"}`

**text** is the text user want to send, whose length must be less
  than 300(`{"result": "badMessageText"}` otherwise)

###selectRace
#####Format:
    {
      "action": "selectRace",
      "sid": <sid>,
      "position": <position>
    }
#####Success:
       {"result": "ok", "tokenBadgeId": tokenBadgeId}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badUserSid"},
      {"result": "badPosition"},
      {"result": "badMoneyAmount"},
      {"result": "badStage"}

      
#####Description:
Selects a race with position <position> on the desk. User can't 
do pretty much anything without the choosing a race first. The race 
he chose will be referred as his ``Token Badge'' later in this text.
Choosing a race while having the active race results in {"result": "badStage"}`

**Sid** must be a valid session id of one of users playing in game,
otherwise it returns`{"result": "badUserSid"}`


**position** is a position of token badge on the desk. It must not be
negative and less or equals 5, otherwise the function
returns`{"result": "badPosition"}`

When you choose race you have to pay one coin for every race with
position higher than position of race you choose. If you haven't
enough coins to do it, the result will be `{"result":
"badMoneyAmount"}`.

"coins to pay" = "position"

You can choose race only when you have no active race, otherwise it
returns`{"result": "badStage"}`

###conquer
#####Format:
    {
      "action": "conquer",
      "sid": <sid>,
      "regionId": <regionId>
    }
#####Success:
    {"result": "ok", "dice": <dice> //optional}
      
#####Fail:
    {"result": "badJson"},
    {"result": "badRegionId"},
	{"result": "badUserSid"},
    {"result": "badRegion"},
    {"result": "regionIsImmune"},
    {"result": "badStage"}
      

#####Description:
Conquers the region with id <regionId>. User can't conquer region if he has no race.
If he attacked some other player's badge as the previous action and there were
more than one unit in attacked army,
he ought to wait for that guy to defend before doing anything.
Violation of these rules ensures you getting `{"result": "badStage"}` as response.
If the region has dragon, hero or hole in the ground, it's immune to attacks and 
trying to conquer it gets you `{"result": "badRegion"}`. This error also
occurs when user tries to conquer the region with the same race 
he has as his active one. 

To conquer  a region, you should sent there a
number of units equal to the region's conquering 
price. Basic region conquering price is 2.
It's incremented with the number
of enemy's units in the regions. The mountain
in a region increments it's defense by 1.
The price can be decreased with your race and 
special power
abilities and increased with enemy's abilities.
The region's defense can't be less than one.


**sid** must be a session id of the current player, otherwise
`{"result": "badStage"}`

**regionId** must be a valid region id of current map, otherwise
  function returns `{"result": "badRegionId"}`.

User cannot attack the same token badge, and he can attack regions accordingly to rules, otherwise 
If the region has dragon, hero or hole in the ground, function returns `{"result": "badRegion"}`.
This command can only be executed after followng commands: ["conquer",
"selectRace", "finishTurn", "throwDice", "defend"].

User cannot attack if the attacked in previous command user didn't
defend, but he could(there were tokens for redeployment), otherwise
`{"result": "badStage"}`.

If user hasn't enough tokens for the conquering, he throws the dice,
thus it would be his conquer on this turn. In this case function also
returns "dice": <dice>


###decline
#####Format:
    {
      "action": "decline",
      "sid": <sid>
    }
#####Success:
    {"result": "ok"}
      
#####Fail:
    {"result": "badJson"},
    {"result": "badUserSid"},
    {"result": "badStage"}

#####Description:
Sends the current user race in decline. Player can only execute this
command in the begging of turn, unless he has ``Stout'' special power
in which case he can do it after his redeployment. In result of this 
command, user's current Token Badge gets 'declined' status. He can't
do nothing but finish turn after executing and in the beginning
of the next turn he chooses his next race and special power. 
User can conquer the regions of his declined Token Badge again.
User still receives the money from the regions with this Token Badge.
If player had another Token Badge before executing this command,
every unit of this badge dies and player loses these regions.

**sid** must be a session id of the current player, otherwise
`{"result": "badStage"}`

User may go in decline only if he has active race, otherwise
`{"result": "badStage"}`

This command can be only executed after following commands:
[finishTurn, redeploy/*only for Stout special power*/], otherwise
`{"result": "badStage"}`


###redeploy
#####Format:
    {
      "action": "redeploy",
      "sid": <sid>,
      "regions": [{"regionId": <regionId>, "tokensNum": <tokensNum>}],
      "encampments": [{"regionId": <regionId>, "encampmentsNum": <encampmentsNum>}],
      "fortified": {"regionId": <regionId>},
      "heroes": ["regionId": <regionId>]
    }
#####Success:
     {"result": "ok"}
      
#####Fail:
    {"result": "badJson"},
	 {"result": "badUserSid"},
    {"result": "badRegionId"},
    {"result": "badRegion"},
    {"result": "noTokensForRedeployment"},
    {"result": "userHasNotRegions"},
    {"result": "badTokensNum"},
    {"result": "badEncampmentsNum"},
    {"result": "tooManyFortifiedsInRegion"},
    {"result": "tooManyFortifiedsOnMap"},
    {"result": "tooManyFortifieds"},
    {"result": "notEnoughEncampentsForRedeployment"},
    {"result": "badSetHeroCommand"}
      
#####Description:
Distributes the units on the map. It can only be executed in the
end of the turn, and it's mandatory to execute it. Every user's 
unit taken from his region and placed accordingly to <regions> list.
If some of the user's region doesn't receive its share of troops
as this command's argument, the user loses this region.

**sid** must be a session id of the current player, otherwise
`{"result": "badStage"}`

**regions** must be a list of regions that belong this user and have
his current race. The sum of tokensNum must be **equal** to the
current total number of tokens, otherwise 
`{"result": "badTokensNum"}`. If user has not tokens, it returns  
`{"result": "userHasNotRegions"}`. 

**encampments** field can be executed only by user with Bivouacking
special power. It consists of the list of regions that belong this
user with this special power. Sum of encampmentsNum must not be
greater than 5, otherwise it returns  
`{"result": "notEnoughEncampentsForRedeployment"}`. 

**fortified** field can be executed only by user with special power
Fortified. It consists of only one field -- reftifield, where user
wants to set the fortress. It can be the region of this user and this
race, otherwise -- `{"result": "badRegion"}`. User can set only on
fortress per regions, otherwise -- 
`{"result": "tooManyFortifiedsInRegion"}`. User cannot set more than current
maximum number of fortresses, otherwise -- 
`{"result": "tooManyFortifieds"}`

**heroes** allows user with special power Heroic set two heroes on his
  regions. If the number of heroes greater than 2, it returns
  `{"result": "badSetHeroCommand"}`. 


This command can be executed only after following commands: "conquer", "throwDice", "defend"

###selectFriend
####Format:
    {
        "action": "selectFriend",
        "sid": <sid>,
        "friendId": <friendId>
    }
    
#####Success:
    {"result": "ok"}
      
#####Fail:
    {"result": "badFriendId"},
    {"result": "badFriend"}

#####Description:     
Allows user with special power Diplomat choose the friend. 

**friendId** must be id of the user, that plays in the same game,
otherwise `{"result": "badFriendId"}`. It must be the id of user who
wasn't attacked by current user on this turn, otherwise --  
`{"result": "badFriend"}`.


###finishTurn
#####Format:
    {
      "action": "finishTurn",
      "sid": <sid>
    }
#####Success:
     {"result": "ok", "coins": <coins>, "nextPlayer": <nextPlayer>}
      
#####Fail:
    {"result": "badJson"},
    {"result": "badUserSid"},
    {"result": "badStage"}

#####Description:
Finishes the turn for current player.
In the end of turn, player receives
the number of coins calculated from 
number of his regions plus different
bonuses from his current race
and special power. Then the next player is chosen
according to priority. If there isn't any players 
with higher priority, the game finishes its turn
and user with lowest priority
becomes the active player.

**sid** must be a session id of the current player, otherwise
`{"result": "badStage"}`

It can only be executed after "decline" and "redeploy" actions. It
returns the current number of user coins -- <coins>. 

**<nextPlayer>** -- the identificator  of the next player. If it's the
  last player and last turn, it doesn't return <nextPlayer>.

**<coins>** -- the number of coins of the next player. 


###defend
#####Format:
    {
      "action": "defend",
      "sid": <sid>,
      "regions": [{"regionId": <regionId>, "tokensNum": <tokensNum>}]
    }
#####Success:
    {"result": "ok"}
      
#####Fail:
    {"result": "badJson"},
    {"result": "badUserSid"},
    {"result": "badStage"},
    {"result": "badRegionId"},
    {"result": "badRegion"},
    {"result": "badTokensNum"},
    {"result": "notEnoughTokens"},
    {"result": "thereAreTokensInTheHand"}

#####Description:
If one of the players conquered another player's region as his previous action 
and there were more than one unit there,
that player should immediately ``defend'' his region. Which means he 
should take all his troops from this region and redeploy them among his current
Token Badge's terriories. 
If unadjacent to this region terriories exist, only these are 
allowed for redeployment. After this command is executed,
the control flow returns to the player who
was attacking. 

**sid** must be a session id of the current player, otherwise
`{"result": "badStage"}`

**regions** is a list of regions belonging to the active user with the
current race, otherwise `{"result": "badRegion"}`. If the user has
regions that aren't adjacent to attacked regions, he cannot move
tokens to the regions adjacent to the attacked region, otherwise --
`{"result": "badRegion"}`. 

**<tokensNum>** -- the number of tokens to be
moved to the region with regionId. The sum of <tokensNum> must be

**equal** to the number of attacked tokens, otherwise the function
returns `{"result": "notEnoughTokens"}` or 
`{"result": "thereAreTokensInTheHand"}`. This command can only be executed after
conquering the region that belongs this user with this race. 


###dragonAttack
#####Format:
    {
      "action": "dragonAttack",
      "sid": <sid>,
      "regionId": <regionId>
    }
#####Success:
    {"result": "ok"}
      
#####Fail:
    {"result": "badJson"},
    {"result": "badUserSid"},
    {"result": "badStage"},
    {"result": "badRegionId"},
    {"result": "badRegion"}

#####Description:
Attacks a region with a dragon.
Can only be executed with special power Dragon master, otherwise
`{"result": "badStage"}` If the user conquered a region
with this superpower, this region's considered immune to
other attacks as long as user isn't in decline.

**sid** must be a session id of the current player, otherwise
  `{"result": "badStage"}`

**regionId** must be a region of another tokenBadge, otherwise
  `{"result": "badRegion"}`

Can only be executed after "conquer", "selectRace", "finishTurn",
"throwDice", "defend", otherwise `{"result": "badStage"}`


###enchant
#####Format:
    {
      "action": "enchant",
      "sid": <sid>,
      "regionId": <regionId>
    }
#####Success:
    {"result": "ok"}
      
#####Fail:
    {"result": "badJson"},
    {"result": "badUserSid"},
    {"result": "badStage"},
    {"result": "badRegionId"},
    {"result": "badRegion"},
	{"result": "badAttackedRace"},
	{"result": "nothingToEnchant"},
	{"result": "cannotEnchantDeclinedRace"},
	{"result": "noMoreTokensInStorageTray"}
	

#####Description:
Enchants the enemy region. Enemy unit's taken
from the region and replaced with your unit.
Can  only be executed with the race Sorcerers, 
otherwise `{"result": "badStage"}` Only another player's region
can be enchanted and only one unit should be here, 
otherwise you get `{"result": "badRegion"}`
If you're trying to enchant your own region,
enjoy `{"result": "badAttackedRace"}`.
If there ain't any units there' you get `
{"result": "nothingToEnchant"}`.
You can only enchant active race, otherwise
`{"result": "cannotEnchantDeclinedRace"}`.
Every race has the maximum number of troops, 
so beware of `"noMoreTokensInStorageTray"` error.

 
**sid** must be a session id of the current player, otherwise
`{"result": "badStage"}`

**regionId** must be a region of another tokenBadge that race isn't in
decline and there is only one token on this region, otherwise
`{"result": "badRegion"}`

Can be executed only after "conquer", "selectRace", "finishTurn",
"throwDice", "defend", otherwise `{"result": "badStage"}`


###throwDice
#####Format:
    {
      "action": "throwDice",
      "sid": <sid>
    }
#####Success:
    {"result": "ok"}
      
#####Fail:
    {"result": "badJson"},
    {"result": "badUserSid"},
    {"result": "badStage"}

#####Description:
Throws the dice before conquering. 
The privelege of user with special power Berserk, another
user gets `{"result": "badStage"}`. The price of region's 
conquering reduces by number of the dice.
After throwing,
you should immediately conquer this region,
and don't tell we didn't warn you.

**sid** must be a session id of the current player, otherwise
`{"result": "badStage"}`

**regionId** must be a region of another tokenBadge that race isn't in
decline and there is only one token on this region, otherwise
`{"result": "badRegion"}`

Can be executed only after "selectRace", "finishTurn", "conquer",
"defend", otherwise `{"result": "badStage"}`

###getGameList
#####Format:
	{
		"action": "getGameList"
	}
#####Success:
	{
		"result": "ok",
		"games": [{
			"gameId": <gameId>, 
			"gameName": <gameName>, 
			"gameDescr": <gameDescr>, 
			"maxPlayersNum": <maxPlayersNum>, 
			"activePlayerId": <activePlayerId>,
			"state": <state>,
			"turn": <turn>,
			"turnsNum": <turnsNum>,
			"mapId": <mapId>,
			"players": [
				{
					"userId": <userId>, 
					"username": <username>, 
					"isReady": <isReady>
				}]
		}]
	}
#####Description:
Receives the list of games.
	**activePlayer** -- id of activePlayer
	**maxPlayersNum** -- number of players in map description
	**turnsNum** -- number of this map turns, 
	**state** can take one of the following values:
		GAME_WAITING = 1
		GAME_PROCESSING = 2
		GAME_ENDED = 3
	**turn** -- current turn
	
	
###getGameState
#####Format:
	{
		"action": "getGameState",
		"gameId": <gameId>
	}
#####Fail:
	{
		"result": "badGameId"
	}
#####Success:
	{
		"result": "ok",
		"gameState": {
			"gameId": <gameId>, 
			"gameName": <gameName>, 
			"gameDescription": <gameDescription>, 
			"currentPlayersNum": <currentPlayersNum>, 
			"activePlayerId": <activePlayerId>,
			"state": <state>,
			"currentTurn": <currentTurn>,
			"map": {
				"mapId": <mapId>,
				"mapName": <mapName>,
				"turnsNum": <turnsNum>,
				"playersNum": <playersNum>,
				"regions":[{
					"constRegionState": [<constRegionParams>],
					"adjacentRegions":[<adjacentRegion>],
					"currentRegionState": {	
						"ownerId": <ownerId>,
						"tokenBadgeId": <tokenBadgeId>,
						"tokensNum": <tokensNum>,
						"holeInTheGround": <holeInTheGround>,
						"encampment": <encampmentsNum>, 
						"dragon": <dragon>, 
						"fortified": <fortified>,
						"hero": <hero>,
						"inDecline": <inDecline>
					}
				}]
			},
			"players": [
				{
					"userId": <userId>, 
					"username": <username>, 
					"isReady": <isReady>,
					"coins": <coins>,
					"tokensInHand": <tokensInHand>,
					"priority": <priority>,
					"currentTokenBadge": {
						"tokenBadgeId": <tokenBadgeId>, 
						"totalTokensNum": <totalTokensNum>, 
						"raceName": <raceName>,
						"specialPowerName": <specialPowerName>
					},	//optional
					"declinedTokenBadge": {
						"tokenBadgeId": <tokenBadgeId>, 
						"totalTokensNum": <totalTokensNum>, 
						"raceName": <raceName>,
						"specialPowerName": <specialPowerName>
					},
				}],
			"visibleTokenBadges": [{
				"raceName": <raceName>, 
				"specialPowerName": <specialPowerName>, 
				"position": <position>, 
				"bonusMoney": <bonusMoney>}]
		}
	}
#####Description:
	**visibleTokenBadges** returns 6 visible pairs of races and specialPowers, their positions and bonus money.
    First pair cost 0 coins, second -- 1 coins etc.
	
###getMapList
#####Format:
	{
		"action": "getMapList"
	}
#####Success:
	{
		"result": "ok",
		"maps": [{
			"mapId": <mapId>,
			"mapName": <mapName>,
			"turnsNum": <turnsNum>,
			"playersNum": <playersNum>,
			"picture": <image>
			"regions":[{
				"constRegionState": [<constRegionParams>],
				"coordinates":[[<x>,<y>]],
				"raceCoords":[<x>,<y>],
				"powerCoords":[<x>,<y>]
			}]
		}]
	}
#####Description:
Receives the list of maps.
all coordinates are number of pixels. (0, 0) - left top corner

###saveGame
#####Format:
	{
		"action": "saveGame",
		"gameId": <gameId>
	}
#####Fail:
	{
		"result": "badGameId"
	}
#####Success:
	{
		"result": "ok",
		"actions": [<sequence of json commands, where sid replaced to userId>]
	}
#####Description:
    In action "createGame" should be field "randseed".

###loadGame
#####Format:
	{
		"action": "loadGame",
		"actions": [<sequence of json commands, where sid replaced to userId>]
	}
#####Fail:
    {
        "result": "userNotLoggedIn"
    }
    {
        "result": "badActions"
    }
	{
		"result": "illegalAction"
	}
	//another possible error messages 
#####Success:
	{
		"result": "ok",
	}
#####Description:
	"result": "illegalAction" if action in command is one of 
	the following: 'register', 'uploadMap', 'login', 
	'logout', 'saveGame', 'loadGame', 'resetServer', 
	'createDefaultMaps'
    "result": "userNotLoggedIn" if some user from game not
    logged in yet
    "result": "badActions" if not exists field "actions" or
    it's not list
	
VI.  RACE DESCRIPTION
========================

TRIVIA
-------------
While playing, player's uses different combination
of race and special power. Such combination is 
called ``Token Badge``. Various kinds of races and
special powers gives the user
different bonuses. Player can throw his Token Badge
away when going in decline, he has to choose
the another race in the beginning of the next
turn then.



