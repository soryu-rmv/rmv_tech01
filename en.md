
# Efficient design for events in RPG story by using four js plugins
  
by @soryu_rpmakermv  
----------------------------  


## 1. Introdcution
In RPGmakers, we treat various process seen in RPG; arrangement of characters on a scene, their actions, objects like doors, and other internal processes for games as events. 
Particularly, we often make events which have a character sprite in RPGmakers.
They naturally require processes of convasation, movement actions, and management of switches (flags) in RPG.
Amount of such works are propotional to the scale of developing game in terms of number of maps, and characters.

![Fig1](https://user-images.githubusercontent.com/64351233/80562186-ee03c600-8a21-11ea-8873-32777155dcb8.png)

Above figure is a RPGMV scene presents a map for RPG under construction.  
In usually RPGmaker style, we assign one event to show one character.  
Of course, those characters are not always visible. By managing switches ON/OFF,  
character sprites are visible only for event scene.

![Fig2](https://user-images.githubusercontent.com/64351233/80562182-eba16c00-8a21-11ea-9fcf-2b975ff9be5e.png)

Above figure shows one of a main event. 
As we can see, it has many event pages because it is recycled to process sevaral event scenes   
on the map by using switches. Other events which have a sprite on the map also have   
several conditional event pages, and are managed by switches.  

Event scenes on RPG story which many characters appear must arrange the character events,   
which is managed correctly by using switches. This clearly becomes hard work in propotion to the scale of game.   

Actually in such cases, we anticipate to occur unexpected bugs in game due to careless management of switches.   
This may affect playability of the game, which leads to disappointment of players.  

In addition to this problem, we have a risk that RPGMV projects crashes when we are making games.
RPGMV extracts the game project data on memory and they seems to be never released until RPGMV is closed.
When we develop large-scale game in RPGMV, we have high risk to be devastated by such crashes   
like force termination of RPGMV during making events and saving current process. 
(I have actually been devastated by such problem, especially destroying of "animation.json".)

Though we cannot optimize behaviors in RPGMV even using js plugins, they are hardly fixed even latest RPGMV version 1.6.2..  
In one sence, we are fighting with crash of RPGMV when we develop large-scale RPG.


To get over the barrier appears in RPGMV, I considered a technique to improve efficiency of event making   
by using below 4 plugins which are distributed online.  
This is effective for the game which many characters appear in event scenes.  

- EventReSpawn.js
- TemplateEvent.js
- Manosasayaki_CharacterBind.js
- varIDforPlugin.js


This attempt makes rpgmakers to relieve hard workd for management of switches for every character event,  
that consequents to reduce number of events arranged on a map greatly.  
As a result, the total amount of data actually saved and loaded via .json files by RPGMV are reduced,  
which enable us to reduce risks of RPGMV crash during construction works.  



## 2. Plugins used in this attempt

This attempts is contributed by following four plugins.  
For their individual basic introduction and detail of usage, access to every distribution site by yourself.   


### - EventReSpawn.js
### - TemplateEvent.js

They are the plugins for dynamic generation of events and their management.
Their works are the core of this attempt.

### - Manosasayaki_CharacterBind.js

This plugin can fix a target event ID of actions called by event command in RPGMV to a specified event ID.


### - varIDforPlugin.js

This plugin enables us to set variables managed by RPGMV to arguments of any plugin commands.  

  
  
  
  
## 3. Event implementation in RPGMV by plugins

In this attempt, I register four plugins like above order. 
Though I have been using more than 120 scripts including development by Yanfly and Moghunter,
I confirm no troubles due to conflict of plugin implementation.


Manosasayaki_CharacterBind.js  [LINK](https://plugin.fungamemake.com/archives/26451)    
EventReSpawn.js  [LINK](https://triacontane.blogspot.com/2016/08/blog-post.html)    
TemplateEvent.js  [LINK](https://triacontane.blogspot.com/2016/06/blog-post_25.html)    
varIDforPlugin.js  [LINK](https://fermiumbay13.hatenablog.com/entry/2017/12/03/102529)    


### Preparation for characters
First, register a event (mainly party actor members) which is to appear on a map scene frequently.  
This is but a regular use of EventReSpawn.js and TemplateEvent.js, we just follow instructions  
presented by the plugin's distributor.


![Fig3](https://user-images.githubusercontent.com/64351233/80565614-e1846b00-8a2b-11ea-9d61-0063ed06cea9.png)

### Description of events in RPGMV

#### Launch

After the preparation, we start make a event on the map as scene event of RPG.
We just make one event which totally process the event such as trigger of event,    
arrangement of character events, conversation, and termination.  

In my case, the position of player is monitored in the first page (parallel process).  
When the player is at specified coordinate, the event scene is actually launched.  

![Fig4](https://user-images.githubusercontent.com/64351233/80565698-198bae00-8a2c-11ea-90f8-2275e985d8d3.png)

After the event trigger is fullfilled, put "Fadeout Screen" (I process that through a common event).
While the screen is black out, **we generate events for character sprites dynamically**.  

By using TemplateEvent.js, we can obtain event ID of generated events for character.
I store obtained ID in variables managed by RPGMV.
The first character (hero) is variable 1, the second is var. 2, and the thrid is var. 3.

On an event screen in the figure (sorry currently in Japanese), events for three characters  
arranged at specified coordinates. Each event ID is stored in a designated variable.  

For just a dynamic event generation and arrangement, this ends now.
However, there remains significant problems in terms of **"direction of RPG event scenes"**.


![Fig5](https://user-images.githubusercontent.com/64351233/80565699-1abcdb00-8a2c-11ea-98b7-5a0d0c4ed41c.png)


#### Targetting on generated events for moving event commands

The problem relies on direction of RPG event scenes is that  
we **cannot specify dyanamically generated events ID** to make actions and speech baloons above them by using RPGMV.
As a matter of course, events do not exist on RPGMV editor because they are generated during the game.


We then specify target event ID by using a plugin command presented by Manosasayaki_CharacterBind.js.  
But, unfortunately, event ID to be specified by this command also **must be constant ID** .  


Here it is a turn of varIDforPlugin.js eventually.  
After installation of this plugin, we can use following suffix of every plugin command.

**_varID[X]**

With this suffix, **X-th argument of the plugin command is replaced to the variable in RPGMV whose ID is the value of X-th argument** .  
Being every event ID which is generated dinamically known, we can write down commands as below.   

**CharacterBind_varID[1] 1**  

This command stands for a command **targetting an event represented by variable 1**.
Therefore, when we insert a event command "Turn Down" in Set Movement Route after this command,
the command replaced to the 1st character will turn below (even if the target is specified in Set Movement Route command).



![Fig7](https://user-images.githubusercontent.com/64351233/80568648-62defc00-8a32-11ea-828b-72a8397a32c3.png)

The CharacterBind command affects only event commands to specify target in RPGMV such as   
Set Movement Route command, Show Ballon icon, Show animation.
(Show text is not affected by this.)  


Before such commands require the target, put the command like  
CharacterBind_varID[1] 2 , then it is a specification of target event.  
Unless overwriting ID, The effect of CharacterBind is kept.  
The above figure shows an example of an event scene design for RPG in this technique.   



![Fig6](https://user-images.githubusercontent.com/64351233/80568399-ddf3e280-8a31-11ea-8ca8-b68ab82d4d4e.png)

Finally, I succeeded to optimize the process that when the player reaches a specific point on a map,   
triger an event with dynamic arrangement of character sprites.   
(In an example in above figure, when a player stands at a region encircled by red color,  
the story telling starts after the character sprites are arranged.   
But number of events which control the scene is just only one!)   




#### Delete events

Before terminating scene event, we must delete the dynamically generated events for character sprites,  
or character sprites will still remain after the event, which seemes to be pity.


By using script in RPGMV command, we can delete events as follows.

$gameMap.eraseEvent($gameVariables.value(1))  
$gameMap.eraseEvent($gameVariables.value(2))  
$gameMap.eraseEvent($gameVariables.value(3))   

It is exactly convenient for large number of events to write as below.

for(var i=1; i<=3; i++){  
　$gameMap.eraseEvent($gameVariables.value(i))   
}  



Moreover, target fix by Manosasayaki_CharacterBind.js is valid unless we specify to stop.  
With delete the generated events, call a plugin command like below.

**CharacterBind**  

If you forget that, every event behavior on game scene becomes crazy.





## 4. Assosiation with other plugins

I introduce additional assosiation with other plugins which are avaiable for free   
to strengthen direction of RPG event scenes.


### YEP_MessageCore.js
Since we have every event ID in variables by TemplateEvent.js,   
we obtain easy management of the ballon message window provided by YEP_MessageCore. 
For the ballon message window With YEP_MessageCore, we have to specify event ID to show message window
above specific characters. The event ID for character is generally different in each map,   
which leads to making a bug due to mismanagement of event.   
(ID is assigned in the order of event generation in the map.)    

However, with this technique, we can the event ID for 1st character by specifing **\v[1]**.
This is much easier as usual. (We can recycle by copy & paste from message commands in other events.)    

### SRD_CameraCore.js

There are several plugins for the treatment of camera view on the map.   
The project shown as an example uses SRD_CameraCore.js and its supportive plugins.   

FocusCamera_varID[2] event 1 1   

Above plugin command works to focus the camera to the event whose ID is the value of variable 1.   
This is realized by co-working with varIDforPlugin.js.  




## Appendix

After this article is released, **Manosasayaki_CharacterBind.js is updated**    
**so as to assign an argument with a variable ID** (in May 2, 2020). By this update, we are able to   
implement this technique in this article with three plugins.

Nevertheless, an option to assign arguments with variables are not applied to existing plugins in general.
In terms of using other plugin combinations as shown in Section 4, varIDforPlugin.js is still effective.
