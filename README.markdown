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
      "playersNum": <playersNum>,
	  "turnsNum": <turnsNum>,
	  "regions": [{"population": <population>, "landDescription": [<landDescription>], "adjacent": [<adjacentRegion>]}]
    }
#####Success:
      {"result": "ok", "mapId": <mapId>}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badMapName"},
      {"result": "badPlayersNum"},
	  {"result": "badTurnsNum"}
      
#####Description:
Creates a new map with mapName name, name must be UNIQUE string 
with length less than 16 symbols, otherwise returns `{"result":
"badMapName"}`. 

**playersNum** is an integer in interval[2, 5](otherwise `{"result": "badPlayersNum"}`)
  
**turnsNum** is an integer in interval[5, 10](otherwise `{"result": "badTurnsNum"}`) 
  
**regions** is a list of regions description. All regions in list are numerated in ascending order, starting from 1.
Each description **must** include list of **adjacent regions**(number of regions), 
and may  include **population** -- initial number of tokens of lost tribes and  
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
	]
###createGame
#####Format:
    {
      "action": "createGame",
      "sid": <sid>,
      "gameName": "<gameName>",
      "mapId": <mapId>
      "gameDescr": "<gameDescription>" //optional
    }
#####Success:
      {"result": "ok", "gameId": <gameId>}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badSid"},
      {"result": "badMapId"}
      {"result": "badGameName"},
      {"result": "badGameDescription"}
      
#####Description:
Creates new game. 

**Sid** must be a valid session id of one of users not playing any
  other game, otherwise returns `{"result": "badSid"}`

**gameName** must be **UNIQUE** string whose length is in interval [1,
  50], otherwise function returns `{"result": "badGameName"}`. 

**mapId** must be valid id of map, otherwise returns {"result":
  "badMapId"}

**playersNum** is an integer that is equal to playerNum of chosen
  map(otherwise `{"result": "badnumberOfPPlayers"}`)

**gameDescription** is an optional field, whoset length must be less
  than 300(`{"result": "badGameDescription"}` otherwise)

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
	 			'adjacent' : [1, 16, 17] 
	 		},
	 		{
	 			'landDescription' : ['mine', 'border', 'coast', 'forest'], #2
	 			'adjacent' : [0, 17, 18, 2] 
	 		},
	 		{
	 			'landDescription' : ['border', 'mountain'], #3
	 			'adjacent' : [1, 18, 20, 3] 
	 		},
	 		{
	 			'landDescription' : ['farmland', 'border'], #4
	 			'adjacent' : [2, 20, 21, 4] 
	 		},
	 		{
	 			'landDescription' : ['cavern', 'border', 'swamp'], #5
	 			'adjacent' : [3, 21, 22, 5] 
	 		},
			{
				'population': 1,
	 			'landDescription' : ['forest', 'border'], #6
	 			'adjacent' : [4, 22, 6] 
	 		},
			{
	 			'landDescription' : ['mine', 'border', 'swamp'], #7
	 			'adjacent' : [5, 22, 7, 23, 25] 
	 		},
	 		{
	 			'landDescription' : ['border', 'mountain', 'coast'], #8
	 			'adjacent' : [6, 25, 9, 8, 23] 
	 		},
	 		{
	 			'landDescription' : ['border', 'sea'], #9
	 			'adjacent' : [7, 9, 10] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['cavern', 'coast'], #10
	 			'adjacent' : [8, 7, 10, 25] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['mine', 'coast', 'forest', 'border'], #11
	 			'adjacent' : [9, 25, 26, 11] 
	 		},
	 		{
	 			'landDescription' : ['forest', 'border'], #12
	 			'adjacent' : [10, 26, 29, 12] 
	 		},
	 		{
	 			'landDescription' : ['mountain', 'border'], #13
	 			'adjacent' : [11, 29, 27, 13] 
	 		},
	 		{
	 			'landDescription' : ['mountain', 'border'], #14
	 			'adjacent' : [12, 27, 15, 14] 
	 		},
	 		{
	 			'landDescription' : ['hill', 'border'], #15
	 			'adjacent' : [13, 15] 
	 		},
	 		{
	 			'landDescription' : ['farmland', 'magic', 'border'], #16
	 			'adjacent' : [14, 19, 27, 16] 
	 		},
	 		{
	 			'landDescription' : ['border', 'mountain', 'cavern', 'mine', #17 
	 				'coast'],
	 			'adjacent' : [15, 19, 0, 17] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['farmland', 'magic', 'coast'], #18
	 			'adjacent' : [16, 19, 0, 18] 
	 		},
	 		{
	 			'landDescription' : ['swamp'], #19
	 			'adjacent' : [17, 2, 20, 1, 19] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['swamp'], #20
	 			'adjacent' : [18, 27, 28, 20] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['hill', 'magic'], #21
	 			'adjacent' : [19, 28, 2, 3, 21] 
	 		},
	 		{
	 			'landDescription' : ['mountain', 'mine'], #22
	 			'adjacent' : [20, 24, 28, 3, 4, 22] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['farmland'], #23
	 			'adjacent' : [21, 24, 5, 4, 23] 
	 		},
	 		{
	 			'landDescription' : ['hill', 'magic'], #24
	 			'adjacent' : [22, 25, 6, 24, 7] 
	 		},
	 		{
	 			'landDescription' : ['mountain', 'cavern'], #25
	 			'adjacent' : [23, 21, 22, 28] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['farmland'], #26
	 			'adjacent' : [24, 23, 6, 7, 9, 10, 26] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['swamp', 'magic'], #27
	 			'adjacent' : [25, 10, 11, 29, 28] 
	 		},
	 		{
	 			'population': 1,
	 			'landDescription' : ['forest', 'cavern'], #28
	 			'adjacent' : [28, 29, 12, 13, 15, 19] 
	 		},
	 		{
	 			'landDescription' : ['sea'],
	 			'adjacent' : [27, 19, 20, 21, 24, 26, 29]  #29
	 		},
	 		{
	 			'landDescription' : ['hill'],  #30
	 			'adjacent' : [28, 27, 12, 11, 26] 
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

**Sid** must be a valid session id of one of the users not playing any
  other game, otherwise the function returns `{"result": "badSid"}`

**gameId** must be a valid id of game, otherwise returns {"result": "badGameId"}
If game status isn't 'waiting', function returns {"result": "badGameState"}.
If the user with specified session id playing or waiting the begining of another game, it returns {"result": "alreadyInGame"}.
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
If user aren't playing any game, it returns {"result": "notInGame"}

**Sid** must be a valid session id of one of the users playing some game, otherwise returns `{"result": "badSid"}`

###setReadinessStatus
#####Format:
    {
      "action": "setReadinessStatus",
      "sid": <sid>,
	  "readinessStatus": <status>,
	  "visibleRaces": [<visibleRace>], 
	  "visibleSpecialPower": [<visibleSpecialPower>]
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
If user aren't playing any game, it returns {"result": "notInGame"}.
User cannot change his readiness status if the game isn't in state 'waiting'(in other game states it returns {"result": "badGameState"})

**Sid** must be a valid session id of one of users who plays in game, otherwise it returns`{"result": "badSid"}`

**status** may be equal to 0 if the user isn't ready, and 1 if he/she is ready to play, otherwise function returns `{"result": "badReadinessStatus"}`  

**visibleRaces** and **visibleSpecialPower** are optional settings used only for testing. They must include the list of names of 6 races and specialPowers. Names must be correct. This commands allows determine what races will be on the desk at the beginning of the game.

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
Get 100 last messages from <since> time

**since** must be a positive float value, otherwise function returns `{"result": "badSid"}`

**messages** is a list of objects such as {"userId": userId, "message": message, "mesTime": mesTime}

###sendMessage
#####Format:
    {
      "action": "sendMessage",
      "sid": <sid>,
	  "text": "<text>"
    }
#####Success:
      {"result": "ok", , "time": time}
      
#####Fail:
      {"result": "badJson"},
      {"result": "badSid"}
      
#####Description:
Send message with <**text**> text from user with sid = <**Sid**>.

**Sid** must be a valid session id of one of users who plays in game, otherwise it returns`{"result": "badSid"}`

**text** is the text user want to send

**time** is the time of sending. In the test mode time for messages is sequence of integers starting with 1

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
      {"result": "badSid"},
      {"result": "badPosition"},
      {"result": "badMoneyAmount"},
      {"result": "badStage"}

      
#####Description:
Select race with position <position> on the desk 

**Sid** must be a valid session id of one of users playing in game, otherwise it returns`{"result": "badSid"}`

**position** is a position of token badge on the desk. It must not be negative and less or equals 5, otherwise the function returns`{"result": "badPosition"}`
When you choose race you have to pay one coin for every race with position higher than position of race you choose. If you haven't enough coins to do it, the result will be `{"result": "badMoneyAmount"}`.

"coins to pay" = "number of visible rases" - "position" - 1 

You can choose race only when you have no active race, otherwise it returns`{"result": "badStage"}`

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
	  {"result": "badRegion"},
	  {"result": "regionIsImmune"},
	  {"result": "badStage"}
	  

#####Description:
**sid** must be a session id of the current player, otherwise {"result": "badStage"}
**regionId** must be a valid region id of current map, otherwise function returns {"result": "badRegionId"}.
User cannot attack the same token badge, and he can attack regions accordingly to rules, otherwise 
If the region has dragon, hero or hole in the ground, function returns {"result": "badRegion"}.
This command can only be executed after followng commands: ["conquer", "selectRace", "finishTurn", "throwDice", "defend"].
User cannot attack if the attacked in previous command user didn't defend, but he could(there were tokens for redeployment), otherwise {"result": "badStage"}.
If user hasn't enough tokens for the conquering, he throws the dice, thus it would be his conquer on this turn. In this case function also returns "dice": <dice>

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
	  {"result": "badSid"},
	  {"result": "badStage"}

#####Description:
**sid** must be a session id of the current player, otherwise {"result": "badStage"}
User may go in decline only if he has active race, otherwise {"result": "badStage"}
This command can be only executed after following commands: [finishTurn, redeploy/*only for Stout special power*/], otherwise  {"result": "badStage"}

###redeploy
#####Format:
    {
      "action": "redeploy",
      "sid": <sid>,
	  "regions": [{"regionId": <regionId>, "tokensNum": <tokensNum>}],
	  "encampments": [{"regionId": <regionId>, "encampmentsNum": <encampmentsNum>}],
	  "fortifield": {"regionId": <regionId>},
	  "heroes": ["regionId": <regionId>]
    }
#####Success:
       {"result": "ok"}
      
#####Fail:
      {"result": "badJson"},
	  {"result": "badRegionId"},
	  {"result": "badRegion"},
	  {"result": "noTokensForRedeployment"},
	  {"result": "userHasNoRegions"},
	  {"result": "badTokensNum"},
	  {"result": "badEncampmentsNum"},
	  {"result": "tooManyFortifieldsInRegion"},
	  {"result": "tooManyFortifieldsOnMap"},
	  {"result": "tooManyFortifields"},
	  {"result": "notEnoughEncampentsForRedeployment"},
	  {"result": "badSetHeroCommand"}
	  
#####Description:
**sid** must be a session id of the current player, otherwise {"result": "badStage"}
**regions** must be a list of regions that belong this user and have his current race. The sum of tokensNum must be **equal** to the current total number of tokens, otherwise {"result": "badTokensNum"}. If user has not tokens, it returns  {"result": "userHasNotRegions"}. 
**encampments** field can be executed only by user with Bivouacking special power. It consists of the list of regions that belong this user with this special power. Sum of encampmentsNum must not be greater than 5, otherwise it returns  {"result": "notEnoughEncampentsForRedeployment"}. 
**fortifield** field can be executed only by user with special power Fortifield. It consists of only one field -- reftifield, where user wants to set the fortress. It can be the region of this user and this race, otherwise -- {"result": "badRegion"}. User can set only on fortress per regions, otherwise -- {"result": "tooManyFortifieldsInRegion"}. User cannot set more than current maximum number of fortresses, otherwise -- {"result": "tooManyFortifields"}
**heroes** allows user with special power Heroic set two heroes on his regions. If the number of heroes greater than 2, it returns {"result": "badSetHeroCommand"}. 

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
Allows user with special power Diplomat choose the friend. friendId must be id of the user, that plays in the same game, otherwise {"result": "badFriendId"}. It must be the id of user who wasn't attacked by current user on this turn, otherwise --  {"result": "badFriend"}.

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
	  {"result": "badSid"},
	  {"result": "badStage"}

#####Description:
**sid** must be a session id of the current player, otherwise {"result": "badStage"}
It can only be executed after "decline" and "redeploy" actions. It returns the current number of user coins -- <coins>. <nextPlayer> -- the identificator  of the next player. If it's the last player and last turn, it doesn't return <nextPlayer>.

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
	  {"result": "badSid"},
	  {"result": "badStage"},
	  {"result": "badRegionId"},
	  {"result": "badRegion"},
	  {"result": "badTokensNum"},
	  {"result": "notEnoughTokens"},
	  {"result": "thereAreTokensInTheHand"}

#####Description:
**sid** must be a session id of the current player, otherwise {"result": "badStage"}
**regions** is a list of regions belonging to the active user with the current race, otherwise {"result": "badRegion"}. If the user has regions that aren't adjacent to attacked regions, he cannot move tokens to the regions adjacent to the attacked region, otherwise -- {"result": "badRegion"}. <tokensNum> -- the number of tokens to be moved to the region with regionId. The sum of <tokensNum> must be **equal** to the number of attacked tokens, otherwise the function returns {"result": "notEnoughTokens"} or {"result": "thereAreTokensInTheHand"}. This command can only be executed after conquering the region that belongs this user with this race. 

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
	  {"result": "badSid"},
	  {"result": "badStage"},
	  {"result": "badRegionId"},
	  {"result": "badRegion"}

#####Description:
Can only be executed with special power Dragon master, otherwise {"result": "badStage"}
**sid** must be a session id of the current player, otherwise {"result": "badStage"}
**regionId** must be a region of another tokenBadge, otherwise {"result": "badRegion"}
Can only be executed after "conquer", "selectRace", "finishTurn", "throwDice", "defend", otherwise {"result": "badStage"}

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
	  {"result": "badSid"},
	  {"result": "badStage"},
	  {"result": "badRegionId"},
	  {"result": "badRegion"}

#####Description:
Can  only be executed with the race Sorcerers, otherwise {"result": "badStage"}
**sid** must be a session id of the current player, otherwise {"result": "badStage"}
**regionId** must be a region of another tokenBadge that race isn't in decline and there is only one token on this region, otherwise {"result": "badRegion"}
Can be executed only after "conquer", "selectRace", "finishTurn", "throwDice", "defend", otherwise {"result": "badStage"}

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
	  {"result": "badSid"},
	  {"result": "badStage"}

#####Description:
Can only be executed with special power Berserk, otherwise {"result": "badStage"}
**sid** must be a session id of the current player, otherwise {"result": "badStage"}
**regionId** must be a region of another tokenBadge that race isn't in decline and there is only one token on this region, otherwise {"result": "badRegion"}
Can be executed only after "selectRace", "finishTurn", "conquer", "defend", otherwise {"result": "badStage"}
