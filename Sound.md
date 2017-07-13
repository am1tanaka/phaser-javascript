# 音声フォーマット
PhaserはWebブラウザー上で動作しますので、どのフォーマットが再生できるかは動作させるWebブラウザーによって異なります。フリー素材サイトでよく見かけるフォーマットであるMP3、OGG、WAVEは以下のような傾向です。

## MP3
ほぼ、再生できます。再生できないのは以下です。

- MP3を再生できない環境でのFirefox
- Chronium(Google Chromeの元になったオープンソースのブラウザー)

## OGG
OGG Vorbisは、InternetExplorerとSafariのいずれも、デスクトップ、モバイルとも未サポートです。

OGG Opusは、ChromeとFirefox以外、未サポートです。

## WAVE
Internet Explorerはデスクトップ、モバイルとも未サポートです。Opera Mobileも未サポートです。

## 以上より
万能なフォーマットはありませんので、`MP3`と`OGG Vorbis`の両対応がよいようです。この2つでおおよそ網羅できるので、容量が大きいWAVEは使わなくてよいでしょう。Phaserでは、以下のようにすることで、指定した2つのファイルのうち、使える方を読み込んでくれます。

```javascript
game.load.audio('BGM', ['assets/audio/bgm.mp3', 'assets/audio/bgm.ogg']);
```

## フォーマット変換
http://media.io/ で、様々なフォーマットに変換できます。MP3、OGG、WAVEのどれか1つしか手元にない場合は、このサービスで足りないフォーマットのデータに変換するとよいでしょう。

## 参考URL
- https://developer.mozilla.org/ja/docs/Web/HTML/Supported_media_formats

# 音データの準備
Phaserのexampleから、`CatAstroPhi_shmup_normal`をループするBGM、`p-ping`と`player_death`を効果音として読み込んでみます。予め、これらの音声ファイルについて http://media.io/ などを使って、`mp3`と`ogg`に変換しておきます。

- http://media.io を開く
- 変換したいファイルをまとめて選択して、[Select Files to Upload]のところにドラッグ＆ドロップする
- 変換したいフォーマット(MP3)を選択
- Select Qualityは[Lower]を選択
- [Convert]ボタンを押す

変換が開始するので、完了するまで待ちます。完了したら[Download as ZIP file]でまとめてダウンロードできます。ZIPを展開して、適当なフォルダーに入れておきます。ここでは、`assets/sounds/mp3`にmp3フォーマットを、`assets/sounds/ogg`にoggフォーマットをコピーしたものとして解説します。

MP3のデータが揃ったら、一度ブラウザーのページを閉じます。改めて http://media.io を開いたら、同様の手順で、コンバート形式を[OGG]にして変換して、データを取り出してフォルダーに配置します。

# 読み込み
## preload
`preload`で、音データを読み込みます。BGMなどのループさせたい音は、この段階でループフラグが設定できます。以下はシンプルな構造の例です。必要に応じて、`game`を`this`に変更してください。

```javascript
function preload() {
    game.load.baseURL = "assets/";

    // BGMをボリューム100%、ループありで読み込み
    game.load.audio("BGM", ["sounds/mp3/CatAstroPhi_shmup_normal.mp3", "sounds/ogg/CatAstroPhi_shmup_normal.ogg"], 1, true);
    
    // SEを読み込み
    game.load.audio("PPING", ["sounds/mp3/p-ping.mp3", "sounds/ogg/p-ping.ogg"]);
    game.load.audio("DEATH", ["sounds/mp3/player_death.mp3", "sounds/ogg/player_death.ogg"]);
}
```

## create
`create`で、そのシーンで利用する音データをゲームに追加します。ここでは単純に全ての音を読み込みます。必要に応じて、変数をインスタンス変数にしたり、`game`を`this`に変更してください。

```javascript
var bgm;
var se_pping;
var se_death;

function create() {
    bgm = game.add.audio("BGM");
    se_pping = game.add.audio("PPING");
    se_death = game.add.audio("DEATH");
}
```

# 音の再生
曲や効果音は、`play()`で鳴らすことができますので、必要な場所で呼び出します。以下、BGMを鳴らす例です。

```javascript
bgm.play();
```

曲をフェードインして開始することができます。第2引数でループするかを設定できます。これで開始する場合は、`play()`は不要です。

```javascript
// 1秒でフェードインして、ループ
bgm.fadeIn(1000, true);
```

# 曲を停止
曲を停止する方法のは3通りあります。

## stop
```javascript
bgm.stop();
```

上記で、曲が停止します。もう一度鳴らすには、`bgm.play();`か`bgm.fadeIn();`を使います。

## pause
```javascript
bgm.pause();
```

これで、曲の再生を一時停止します。`pause`していたら、`bgm.resume();`を呼び出すと中断した場所から曲が再開します。`stop`で停止した場合は、`resume`は機能しません。

## fadeOut
`bgm.fadeOut(ミリ秒);`で、指定した秒数で曲をフェードアウトさせます。

# ボリュームを変更
`bgm.volume = 0.5;`とすると、ボリュームを50%にできます。`0`で音が聞こえなくなり、`1`で最大音量になります。

# 全ての音を制御
`SoundManager`(`game.sound`や`this.sound`)を使うと、全ての音をまとめて制御することができます。以下のようにすれば、曲や効果音など、現在鳴っている全ての音を停止します。

```javascript
game.sound.stopAll();
```

`game.sound.pauseAll();`で一時停止、`game.sound.resumeAll();`で再開です。

`game.sound.volume`プロパティーに0～1の値を設定すれば、全ての音のボリュームを変更できます。

# 状態(StateManager)と音
音データはグラフィックと違って、状態が変わってもゲームワールドから削除されません。何もしないと、次の状態でもそのまま音が鳴り続けます。BGMなどを解放したい場合は`destroy()`を呼び出して手動で破棄します。`destroy(false)`とすると`SoundManager`からは削除されません。

`destroy`では、音データをキャッシュからは消えませんので、`game.add.audio("音名");`で読み直すことができます。

音の読み替えが不要な小さい作品の場合は、破棄はせずに、そのまま使い回せばよいでしょう。音を代入するプロパティーにインスタンスが入っているかをチェックして、入っていなければ読み込みを行い、読み込み済みなら読み込みをスキップさせればよいでしょう。

## 状態の切り替えタイミングでのフェードアウト
`fadeOut()`などの時間がかかる処理は、状態のupdateで制御されています。そのため、実行が完了する前に状態を切り替えてしまうと、そのまま次の状態に移行してしまいます。つまり、フェードアウトを呼び出した直後に状態を切り替えると、フェードアウトが実行されないので曲が鳴り続けたまま、次のシーンに移行してしまいます。

解決策は以下の二通りです。

- 画面の切り替えエフェクトなどを入れて、音のフェードアウトが完了するまでは次の状態に移行しないようにする
- BGMのインスタンスを、`game.state.start();`の第4引数で渡して、次のシーンの`init()`で受け取り、次の状態でフェードアウトを呼び出す

ちゃんと開発する場合は、前者の仕組みを用意するとよいでしょう。シンプルに作る場合は、後者で楽に実装する手もあります。

