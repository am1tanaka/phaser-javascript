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
game.state.start('Title);
```




## まとめ

## 参考書籍
- [Rich & Ilija. Interphase1](https://phaser.io/interphase/1)



