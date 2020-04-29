# 4プラグイン併用によるRPGイベントにおけるキャラクターイベント配置・管理の効率化
(Efficient design for events in RPG story by using four js plugins)

## 1. はじめに
RPGツクールシリーズでは、ゲームマップ上に現れる様々な処理(キャラクターの配置・移動、開閉する扉などの物体、見えない内部的な処理)を「イベント」として扱う。  
特にRPGの作成において、キャラクターの画像を載せたイベントをマップに配置することは頻出の作業である。
キャラクター同士の会話や移動等の動作、それらを起動するスイッチのON/OFFの管理は必ず必要である。  
それらの作業量は、ストーリーの長さ、マップの数、登場キャラクターの数といったゲームの規模に比例して多くなる。  

![Fig1](https://user-images.githubusercontent.com/64351233/80562186-ee03c600-8a21-11ea-8873-32777155dcb8.png)

上図(Fig1)は、RPGの制作過程のマップを表示したプロジェクト画面である。  
ストーリー上に登場するキャラクターを、それぞれ1つのイベントとして配置している。
これらはゲーム中、常に表示状態になっているわけではなく、ストーリーイベント(RPGのシナリオ上のイベントのことを、ツクールのイベントと区別してこう呼ぶことにする)が行われる時にスイッチのON/OFF切り替えにより管理される。  


![Fig2](https://user-images.githubusercontent.com/64351233/80562182-eba16c00-8a21-11ea-9fcf-2b975ff9be5e.png)

さらに上図(Fig2)は、 Fig1中の主要なイベント(キャラクター)の1つを表示したものであるが、  
同じマップで何度もそのキャラクターの表示を使いまわすために、スイッチのON/OFFによるイベントのページ数が多くなっている。  
これと同様なものが、他のキャラクターを管理するイベントにもそれぞれ実装されている。  
  
主要キャラクターが登場するストーリーイベントをこのように実装していく場合、  
ストーリーイベントが発生するマップごとに、同様にイベントを大量に配置し、その表示の有無をスイッチで適切に管理しなければならない。　　
ゲームの規模に比例して、この作業が大変な作業になるということは想像に難くない。  

実際に起こり得るトラブルの１つとして、スイッチON/OFFの管理ミスによる想定外のゲーム上のバグの発生が考えられる。  
これはゲーム性の崩壊に繋がる可能性もあり、ゲームのプレイヤーにとっても問題である。  
もう１つのトラブルとして、ゲームデータの管理のための保存ファイル(json)の容量増大に伴うゲーム制作中のクラッシュが考えられる。  
RPGツクールMVは、プロジェクト内のデータを次々とメモリ上へ展開し、ツクールを終了させない限り際限なくメモリ領域が消費される。  
この状況下において、イベント作成作業途中の強制終了や、プロジェクト保存時の強制終了といったメモリ関連に起因すると思われる深刻な問題を確認している。  
特にプロジェクト保存時の強制終了は、animation.jsonの破壊をも引き起こす可能性があり、ゲーム制作のモチベーションを低下させる事態も避けられない。  
ツクール本体の問題は有志のプラグインで解決できるものでもない上に、最新のver1.6.2でも解決される見通しは無い。 
専ら、長編のRPGの制作となると作業中のツクールのクラッシュとの戦いとなる。


これらのツクールMVによるRPG制作の面倒さや障害を乗り越えるために、  
以下の4つのプラグインを併用した、RPGのキャラクター登場を伴うイベントの配置を効率化を提案する。  

- EventReSpawn.js
- TemplateEvent.js
- Manosasayaki_CharacterBind.js
- varIDforPlugin.js


この手法は、キャラクターごとにスイッチのON/OFFを管理する必要がなくなり、  
マップごとに配置するイベントの総数も大きく減らすことができる。結果的にツクールとして管理するデータ量が減り、  
作業中に発生する問題が起こる危険も低下する。




## 2. 使用プラグインについて
各種プラグインについて簡潔に述べる。詳細や基本的な導入方法は各配布サイトを参照のこととする。  

### - EventReSpawn.js
### - TemplateEvent.js

トリアコンタン氏が作成した、イベントの動的生成・管理に関するプラグインで、本手法の中心的な働きをする。  


### - Manosasayaki_CharacterBind.js

しぐれん氏が作成した、イベントコマンド等によるイベントの動作の対象を「指定したIDのイベントに固定」するプラグインである。  


### - varIDforPlugin.js

フェルミウム湾氏が作成した、プラグインコマンドに与える引数にツクールの変数を指定できるようにするプラグインである。  


  
  
## 3. プラグインによるイベント実装

本実装では、4つのプラグインは以下の順で登録している。  
著名なYEP系やMOG系など120種類以上のプラグインを同時に使用しているが競合等の障害は確認されていない。

Manosasayaki_CharacterBind.js  
EventReSpawn.js  
TemplateEvent.js  
varIDforPlugin.js  


### キャラクターイベント準備
キャラクターに限らないが、頻繁にマップに登場させるイベントを登録する。
これは、EventReSpawn.js や TemplateEvent.js を使っているだけで、ツクール上での作業はプラグイン導入時の説明に従ったままである。

![Fig3](https://user-images.githubusercontent.com/64351233/80565614-e1846b00-8a2b-11ea-9d61-0063ed06cea9.png)

### ストーリーイベントの作成

#### イベントの起動
上記の準備の上で、実際にRPGとしてのイベントを作成する。
イベントの開始条件の管理からキャラクターの配置や会話処理、イベントの終了までを全て１つのイベントで行う。
例えば、下図Fig.4のように、1ページ目ではイベントを起こしたい場所に  
「並列処理」で開始条件（プレイヤーがある場所に立った等）を満たすかどうかを監視する。

![Fig4](https://user-images.githubusercontent.com/64351233/80565698-198bae00-8a2c-11ea-90f8-2275e985d8d3.png)

イベント開始条件を満たしたら、画面のフェードアウト等の処理を入れ（下図Fig.5ではコモンイベント内に含めている）て  
ストーリーイベントに登場するキャラクター等を動的に生成する。

TemplateEvent.js では、動的に作成したキャラクター（のイベント）のIDを直ちに取得できるため、  
これをキャラクターごとにツクールの変数に格納している。  
ここでは、1番目のキャラクターを変数の1番、2番目のキャラクターを変数の2番、…というように保存している。

図に示したイベントでは、キャラクター3体のイベントをマップ上の指定の座標へ動的に配置して、  
イベントのIDはそれぞれ、対応する変数に格納されるようになっている。  
イベントの動的生成、配置だけならばこれで成功であるが、**RPGの演出という点ではまだ問題が残っている。**

![Fig5](https://user-images.githubusercontent.com/64351233/80565699-1abcdb00-8a2c-11ea-98b7-5a0d0c4ed41c.png)

#### 動的生成したキャラクターイベントを各種動作の対象とする

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



#### 動的生成したキャラクターイベントの削除

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


## 4. 他のプラグインとの更なる連携
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
