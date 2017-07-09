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
`loadUpdate`メソッドは、アセットの読み込み中に呼び出されるメソッドです。`update`と同様、マシンの処理速度が十分であれば1秒間に60回程度呼び出されます。アセットの読み込みが終わったら呼び出されなくなり、その代わりに`update`メソッドが呼び出されるようになります。

`loadUpdate`メソッドを使うことで、アセットの読み込み中の演出を表示することができます。ただし、アセットは読み込み中なので使えません。`Graphics`や`Text`など、すぐに使える低レベルの描画関数を利用するとよいでしょう。

### loadRender
`loadUpdate`と同様に、読み込み中に`render`の代わりに呼び出されるメソッドです。

`loadRender`メソッドは、ゲームに追加されたオブジェクトが全て描画された後に呼び出されますので、後処理(Post Processing)による画面効果を加えたり、デバッグテキストを描画するために使えます。

### create
`create`メソッドは、状態が切り替わって、`preload`メソッドが完了し次第、1度だけ呼び出されます。`preload`がない場合は、`init`メソッドが完了し次第呼び出され、`init`メソッドもなければ`create`メソッドが一番最初に実行されます。

`create`メソッドは、その状態に必要なオブジェクトをゲームワールドに準備するために使うのが一般的です。画像やアニメーション、マップなどを追加していきます。

`create`メソッドの実行が完了したら、ゲームのメインループが開始します。このメソッドの実行が終わらないと`update`メソッドは実行されませんので、ここで初期化を完了しておけば、確実に`update`メソッドを実行することができます。

### update
`update`メソッドは、`Phaser`のゲームループによって、1回画面が更新するごとに、自動的に呼び出されます。

`update`メソッドは、`create`メソッドが定義されていた場合、必ず`create`メソッドの実行が完了してから呼び出されます。

`update`メソッドは1秒間に60回ほど実行されますので、何をさせるかを注意深く管理する必要があります。一般的には、物理エンジンによる衝突判定や、入力にキャラクターを反応して動かすことをします。

`update`が呼び出される時の`Phaser`のサブシステムの動作について理解しておくことは重要です。

`Phaser.Game.updateLogic`メソッドを確認すると、以下のようなコードが確認できます。

```javascript
            this.scale.preUpdate();
            this.debug.preUpdate();
            this.camera.preUpdate();
            this.physics.preUpdate();
            this.state.preUpdate(timeStep);
            this.plugins.preUpdate(timeStep);
            this.stage.preUpdate();

            this.state.update();
            this.stage.update();
            this.tweens.update();
            this.sound.update();
            this.input.update();
            this.physics.update();
            this.plugins.update();

            this.stage.postUpdate();
            this.plugins.postUpdate();
```

例えば最初の`this.scale.preUpdate();`は、`Phaser`のシステムのスケール(`scale`)の更新前の処理(`preUpdate()`)を呼び出しています。続いて`debug`、`camera`、`physics`・・・というように、更新の前処理を呼び出しています。

#### stage.preUpdate
このうち特に重要なのが`this.stage.preUpdate();`です。画面に表示される全てのオブジェクトは`Stage`に接続されています。つまり、`this.stage.preUpdate();`によって、ゲームで画面に表示されるスプライトなどのオブジェクトの`preUpdate()`メソッドが呼び出されるのです。

`Phaser.Sprite`の`preUpdate`メソッドは、次の処理のために様々なプロパティーを準備します。例えば、ゲームワールドでのスプライトの座標を設定したり、World Transformを更新したり、更新前の座標を`Sprite.previousPosition`メソッドに代入したり、物理エンジンが有効な場合は、チェックした各種パラメーターをリセットしたりします。

#### 原因と効果
更新前の処理を一通り実行して、パラメーターが準備できたら、最初に呼ばれるのが`this.state.update();`です。ここで実行されるのが、状態オブジェクトの`update()`メソッド、つまり、ここで準備している`update()`メソッドです。その他の`Stage`にある`Sprite`の更新や、`tweens`、`sound`などの更新は、全てこの`state`の更新後に実行されるのです。

例えば、`ArcadePhysics`を持ったスプライトがあって、それと他のスプライトとの衝突判定をするとします。その際、状態オブジェクトの`update()`メソッドで、以下のような衝突コードを書きます。

```javascript
update: function() {
    this.physics.arcade.collide(this.player, this.teki);
}
```

物理エンジンによる衝突ハンドラーはすでに、更新されたスプライトの物理判定を把握しています。なぜなら、それらは全て`Stage.preUpdate()`メソッドで同期されるからです。`collide`メソッドが呼び出されて、必要に応じて二つのスプライトがチェックされて、衝突して、離れます。

#### stage.postUpdate
`preUpdate`と同様のことを、`Stage.postUpdate`は行いますが、この時は画面に残っているもののみを対象とします。

このメソッドは、物理エンジンが有効なスプライトに対して、物理挙動で更新した結果を反映するために使うことができます。もしスプライトがカメラに固定されていた場合、カメラとスプライトの最終的な座標から、スプライトの位置をここで調整します。その他の表示されているオブジェクトは違う動作ですが、その他のオブジェクトそれぞれにフレームの影響が反映して、座標やその他の表示プロパティーが更新されて、レンダーパスの準備が整います。

### preRender
更新が一通り完了した後、描画前に何か処理をしたい場合は、`preRender()`メソッドを利用します。

### render
`render`メソッドは、`Phaser`がディスプレイリストを全て描画し終わった後に呼び出されます。この段階では、Canvasは消されて、ゲームに追加されていたオブジェクトの描画は全て完了しています。

ここは、描画が完了した画面に後処理(Post Processing)効果を加えたり(ブラーなど)、デバッグ表示をしたりするのに利用します。

#### プラグインの描画
Phaserのプラグインを組み込んでいて、それが描画メソッドを持っていた場合、`state`オブジェクトの`render`メソッドが呼ばれたあとに呼ばれます。プラグインが描画した結果に対して、さらに効果を加えたい場合は、プラグインの後に描画されるタイミングにコードを実装する必要があります。

### resize
`resize`メソッドは、`Phaser.ScaleManager`によって呼び出される特別なメソッドです。ゲームのサイズが変わるたびに呼び出されます。ゲームが`Phaser.ScaleManager.RESIZE`スケールモードで動いている時のみ、発生します。

ゲームを組み込むHTML要素のサイズを100%にしていたりすると、ブラウザーのウィンドウサイズを変更するごとにこのメソッドが呼ばれます。

`resize`メソッドは、`width`と`height`の2つの引数を受け取ります。それが新しいゲームのキャンバスサイズです。

### paused
`paused`メソッドは、`Phaser.Game`によって呼び出される特殊なメソッドです。ゲームは、画面に描画されなくなったり、プログラムから`paused`コードを実行した時に、ポーズ状態になります。









## まとめ

## 参考書籍
- [Rich & Ilija. Interphase1](https://phaser.io/interphase/1)



