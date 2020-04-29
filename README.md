# 4プラグイン併用によるRPGイベントにおけるキャラクターイベント配置・管理の効率化
(Efficient design for events in RPG story by using four js plugins)

1. はじめに
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




2. 使用プラグインについて
各種プラグインについて簡潔に述べる。詳細や基本的な導入方法は各配布サイトを参照のこととする。  

- EventReSpawn.js
- TemplateEvent.js

トリアコンタン氏が作成した、イベントの動的生成・管理に関するプラグインで、本手法の中心的な働きをする。  


- Manosasayaki_CharacterBind.js

しぐれん氏が作成した、イベントコマンド等によるイベントの動作の対象を「指定したIDのイベントに固定」するプラグインである。  


- varIDforPlugin.js

フェルミウム湾氏が作成した、プラグインコマンドに与える引数にツクールの変数を指定できるようにするプラグインである。  


  
  
3. プラグインによるイベント実装



4. 他のプラグインとの更なる連携



