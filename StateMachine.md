# ゲームを管理する～状態管理～

## Phaserの状態管理(State Management)
ゲームとして完成させることを考えてみます。プログラムを起動すると、まずはデータを読み込む起動画面が表示されて、次にタイトル画面が表示されて、画面をクリックするとゲームが開始して・・・といったように進行することが思い浮かびます。ゲームに限らず、アプリケーションの開発手法として、機能が異なる場面を<b>状態(State)</b>で分けて、ファイルを分割する方法がよく使われます。

Phaserには、予め状態を分割して作成し、それを切り替える仕組み`State Manager`を持っています。ここでは`State Manager`の使い方を学び、これまで勉強で作ってきたものを、ゲームとして完成させます。

## Phsaerでのシーンの構築方法
### 初期化
Phaserでは、最初に`Phaser.Game`オブジェクトを生成します。小さいプロジェクトでは、以下のように生成と同時に`preload`や`create`といった処理を指定して、すぐにゲームを起動していました。

```javascript
	window.game = new Phaser.Game(640, 360, Phaser.AUTO, 'gameContainer',
		{
			preload: preload,
			create: create,
			update: update,
			render: render
		});
```

State Managerを利用する場合は、`preload`などは状態ごとに違うものを用意しますので、上記の時点では指定しません。以下のようなコードに変わります。

```javascript
window.game = new Phaser.Game(640, 360, Phaser.AUTO, 'gameContainer');

// 状態を登録する
game.state.add('Title', MyGame.Title);
game.state.add('Game', MyGame.Game);
game.state.add('GameOver', MyGame.GameOver);
game.state.add('Clear', MyGame.Clear);

// Titleを起動する
game.state.start('Title');
```

以上で、タイトル、ゲーム、ゲームオーバー、クリアの4つの状態を追加して、それぞれの状態を`MyGame.Title`, `MyGame.Game`, `MyGame.GameOver`, `MyGame.Clear`というオブジェクトを対応付けます。

### 各状態のオブジェクト
状態を管理するオブジェクトとして、タイトル用の`Title.js`を以下に示します。

```javascript

```

## 状態オブジェクトが持っているメソッド
状態オブジェクトに以下のメソッドを用意すると、決まったタイミングでPhaserのシステムがそのメソッドを呼び出してくれます。

- init
- preload
- loadUpdate
- loadRender
- create
- update
- preRender
- render
- resize
- paused
- resumed
- pauseUpdate
- shutdown

### 重要！！
状態オブジェクトに、`preload`, `create`, `update`, `render`の4つのメソッドが1つも定義されていなかった場合、状態マネージャーに読み込まれないので注意してください。それ以外のメソッドは必要がなければ省略して構いません。

### init
状態が開始した直後に、他のメソッドに先駆けて1度だけ実行されるメソッドです。`init`には、`game.state.start()`メソッドの第4引数以降を使ってパラメータを渡すことができます。何かパラメーターを受け取って、初期化したい場合に便利です。

#### 呼び出し例
```javascript
this.state.start("Title", true, false, "Message");
```

#### init例
```javascript
init: function (dt) {
    // Messageとログに表示される
    console.log(dt);
}
```

### preload
`init`の実行が完了したら、次に呼ばれるのが`preload`メソッドです。主に、画像や音声などのリソースを、キャッシュに読み込むために使います。

キャッシュに読み込んだリソースは、`this.state.start()`メソッドの第3引数に`true`を渡さない限り、状態を切り替えても読み込まれたままになります。全てのリソースを起動時に全て読み込めるような小ささのゲームであれば、`Boot`の`preload`メソッドで全てのリソースを読み込んで、それ以外の状態では`preload`は使わなくてよいでしょう。

Phaserのアセット読み込みは非同期処理なので、`preload`メソッドの実行が終わってもリソースの読み込みが完了しているとは限りません。リソースの読み込みが完了すると、`create`メソッドが呼び出されますので、そのような処理は`preload`の最後ではなく、`create`メソッドに実装します。

#### サウンド関連
サウンドファイルは、ファイルを読み込んだあとにさらにデコードをしないと鳴らすことができません。`create`メソッドが開始した時点でファイルの読み込みは完了していますが、デコードが完了しているかは分かりません。デコードが終わるまで待ってから次の状態に移行させるには、`create`メソッドで`setDecordedCallback()`メソッドを使って、サウンドのデコード完了のコールバック関数(ここでは`onSoundLoaded`)を設定して、そこで次の状態を開始するようにします。

```javascript
preload: function () {
    this.load.audio("se", "se.mp3");
},

create: function() {
    var se = game.add.audio("se");
    
    this.sound.setDecordedCallback(
        [ se ],
	this.onSoundLoaded, this
    );
},

onSoundLoaded: function() {
    this.state.start("NextState");
}

```

サウンドのデコードにはCPU能力を大量に使いますので、この間は待ちアニメなどは実行しない方がよいです。

### loadUpdate









## まとめ

## 参考書籍
- [Rich & Ilija. Interphase1](https://phaser.io/interphase/1)



