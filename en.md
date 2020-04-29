
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


Manosasayaki_CharacterBind.js  
EventReSpawn.js  
TemplateEvent.js  
varIDforPlugin.js  


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

RPGの演出という点での問題というのは、  
ツクールのエディタ側からキャラクターを動かしたり、ふきだしアイコンを表示したりする場合に、
**動的生成したキャラクターを指定できない**ということである。  
（ツクール上ではイベントを作っていないことになっているので、**そもそも指定したいキャラクターが存在していない**）  

  
そこで、指定したキャラクターに対してイベントコマンドの処理の対象を固定する  
Manosasayaki_CharacterBind.js を使って、生成したイベントを対象として命令を実行させたい。  
しかし、**このプラグインコマンドで指定するイベントIDも定数でなければならない**ので直接は使えない。  

ここで満を持して、 varIDforPlugin.js の出番となる。このプラグインを導入することによって、  
他の導入済みプラグインのコマンドの後ろに

**_varID[X]**

とつけると、X番目の引数を **「引数で指定した番号のツクールの変数の値」** に変更することができる。  
つまり、今の作業の場合は指定したい動的生成したキャラクターイベントのIDは分かっているので、  
これを用いれば、  

**CharacterBind_varID[1] 1**

のように書けて、これは  
**「1番目の引数の値を番号する変数に格納されたIDのイベント」をコマンド処理の対象とする**　　とできる。  
したがって、直後に来る「このイベント、下を向く」の命令は、「1番のキャラクターを下に向かせる」ことに置き換わる。  


![Fig7](https://user-images.githubusercontent.com/64351233/80568648-62defc00-8a32-11ea-828b-72a8397a32c3.png)

CharacterBind の効果は、移動ルートの設定、フキダシアイコンの表示といった  
ツクール上で対象を指定して用いるイベントコマンドの対象に影響する（文章の表示には影響しない）。  
移動ルートの設定における対象指定はどうでもよく、CharacterBind_varID[1] 2 のように  
コマンドの直前に対象のIDを指定したプラグインコマンドを挿入することで、対象指定の代わりとなる。 
CharacterBindは指定IDを書き換えない限り、効果は持続する。ゆえに、ストーリーイベントの挙動の記述は上図に示す形となる。



![Fig6](https://user-images.githubusercontent.com/64351233/80568399-ddf3e280-8a31-11ea-8ca8-b68ab82d4d4e.png)

これにより、上図の赤丸の領域にプレイヤーが辿り着いたとき、キャラクターを当該位置に置いてストーリーを進行させる  
という処理をツクール上では大きく簡略化することに成功した。



#### Delete events

イベント終了する際には、動的生成したキャラクターを削除しなければならない。  
さもなければ、イベントが終わったにも関わらずキャラクターがその場に残り続けてしまうバグとなる。

これは、イベントコマンドのスクリプトから

$gameMap.eraseEvent($gameVariables.value(1))  
$gameMap.eraseEvent($gameVariables.value(2))  
$gameMap.eraseEvent($gameVariables.value(3))   

のように書けば、1番、2番、3番目のキャラクターに対して「イベントの一時消去」を適用し、生成したイベントを削除できる。

もちろん、数が増えた場合は

for(var i=1; i<=3; i++){  
　$gameMap.eraseEvent($gameVariables.value(i))   
}  

としてもよい。  


また、Manosasayaki_CharacterBind.js によるコマンド対象イベントの固定化は、何もしなければ永続してしまうため  
イベントが終了するタイミングで上記の処理に併せて

CharacterBind

とプラグインコマンド実行して、固定化を解除することを忘れてはならない。

これらの処理は、イベント終了時の「画面のフェードアウト」を行った後に行うとゲーム演出としての自然さが出る。


## 4. Assosiation with other plugins
動的なキャラクターイベント配置において、RPGの演出としての機能強化という点で  
他のプラグインとの共用例も紹介する。

### YEP_MessageCore.js
TemplateEvent.jsにより、生成したイベントのIDを変数に格納しているため、  
YEP_MessageCoreと組み合わせた吹き出しメッセージウィンドウも簡略化が可能である。

マップごとにキャラクターに割り当てられたイベントID（定数）を指定すると、  
マップごとにIDが変わってしまうため、ゲームの規模が大きくなると設定ミスの発生は避けられない。
しかしこの手法によれば、「文章の表示」において1番目のキャラクターのIDは常に **\v[1]** で参照できるため  
会話コマンドのコピーによる再利用の利便性も向上する。


### SRD_CameraCore.js
マップ上でのカメラ機能に関するプラグインは様々なものが公開されているが、
図で例示したプロジェクトでは、SRD_CameraCore.js および関連プラグインを使用している。  

FocusCamera_varID[2] event 1 1

は、2番目の引数（つまり、最初の1）のIDの変数を持つイベントに即座にカメラを合わせる、というプラグインコマンドだが
これも　varIDforPlugin.js との併用で実現している。
