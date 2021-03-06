# 乱数(ランダム)～ゲームの本質～

## そもそも「game\(ゲーム\)」とは

辞書で調べると、「勝敗を競うもの」「遊戯」「駆け引き」など、さまざまな意味があります\(Weblio [http://ejje.weblio.jp/content/game\)。](http://ejje.weblio.jp/content/game%29。)

ゲームの典型的な流れを以下に挙げます。

1. プレイヤーが手を決める
2. 評価する
3. 勝ち負けを判定する
4. 決着がつかなければ1に戻る

ゲームのタイプを考えると、まずは囲碁や将棋、じゃんけんなどの二人以上が参加するゲームが考えられます。これらは、ルールさえ決めれば遊ぶことができます。別のタイプとしては、ソリティアやマインスイーパー、ジェンガなどの一人で遊べるものがあります。

何らかの結果を予想して、プレイヤーは手を決めます。結果が分かっていれば勝負になりませんので、**ゲームには予想が外れる要素が必要**です。対戦ゲームの場合は、お互いに相手の予想しない手で勝ちを狙うので、自然に予想が外れる要素が入ります。一方の一人遊びのゲームでは、トランプを切ったり、爆弾がどこにあるか分からないように配置することで、プレイヤーには予想できない要素を入れます。このように予想できない結果を出すのに必要なのが**乱数**です。

乱数は、トランプをきったり、サイコロを振ったりして求めることができます。コンピューターが登場する遥か以前からゲームを支えてきた道具なのです。

### おまけ：その他に見えるタイプ

詰め碁やパズルなど、一人で遊ぶもので乱数を使わないゲームもあります。アドベンチャーゲームなどもこのタイプでしょう。これらのタイプのゲームでは、プレイヤーが悩むように問題やシナリオを作った人がいます。対面して戦ってはいませんが、出題者が解答者に問題を出しています。解けなければ出題者の勝ち、解ければ解答者の勝ちです。つまり、前者のタイプに入れていいでしょう。

ボーリングやだるま崩しなども乱数は使いません。これらの場合は、配置する時の少しのずれや気象条件による動きの誤差、プレイヤーが完全に同じ動作を繰り返すことはできないというところに乱数が隠されています。

このような乱数を、どこに、どのような形で入れるかによって、ゲームのルールを考えることができます。

## Phaserで乱数を求める方法

0～1未満の乱数は、JavaScriptの`Math.random()`で求めることができますが、ゲームで使いやすいようなものがPhaserに沢山用意されています。よく使いそうなものを紹介します。

### 指定の範囲の整数\(小数がない\)の乱数を求める

```javascript
game.rnd.integerInRange(最小値, 最大値);
```

最小値以上、最大値**以下**の整数の乱数を返します。例えば、`game.rnd.integerInRange(1,6);`とすれば、1～6のサイコロを作ることができます。**最大値を含み**ます。

`game.rnd.between(最小値, 最大値);`が別名で用意されているので、上記は`game.rnd.between(1,6);`と書いても構いません。

### 指定の範囲の実数\(小数がある\)の乱数を求める

```javascript
game.rnd.realInRange(最小値, 最大値);
```

最小値以上、最大値**未満**の実数の乱数を返します。例えば、`game.rnd.realInRange(1,6);`とすると、1～5.9999・・・の乱数が返ります。**最大値が含まれない**ので注意してください。

### 指定の値の中から選ぶ

例えば、1,3,5,9の4つの数字から1つを選ぶようなことが、以下の書き方でできます。

```javascript
game.rnd.pick([1, 3, 5, 9]);
```

アニメーションのところでも出てきましたが、`[`と`]`の間にデータを\(コンマ\)区切りで並べると、配列というものになって、複数のデータを渡すことができます。

### その他の関数

以上を知っていれば問題ないでしょう。その他のものを以下に挙げておきますが、必要になったら参照するということで読み飛ばして構いません。

#### -180～180の整数の乱数を求める

```javascript
game.rnd.angle();
```

角度の乱数を求める場合など。整数である必要はありませんし、-180と180という同じ角度が重複しているため、個人的には`game.rnd.realInRange(-180, 180);`で求めた方がよい気がします。

#### 0以上1未満の乱数を求める

```javascript
game.rnd.frac();
```

#### 0以上2の32乗-1の整数を求める

```javascript
game.rnd.integer();
```

#### -1より大きく、1以下の実数を返す

```javascript
game.rnd.normal();
```

#### 0以上2の32乗未満の実数を求める

```javascript
game.rnd.real();
```

#### -1か1のどちらかを求める

```javascript
game.rnd.sign();
```

#### 決まった範囲のタイムスタンプの乱数を求める

```javascript
game.rnd.timestamp(最小値, 最大値);
```

最小値と最大値にタイムスタンプの値を設定したら、その期間の時間を乱数で求めます。falseなどを渡した場合は、最小値は2000年の最初、最大値は2020年の最後の時間を範囲にとります。

#### RFC4122 Version4 で定義される16進数表記のID文字列を求める

```javascript
game.rnd.uuid();
```

`xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx`という形式のIDを返します。uuidを使う場合に使います。

#### 指定の値の中から、前の方の値が選ばれやすい条件で一つを選ぶ

```javascript
game.rnd.weightedPick([1, 3, 5, 7]);
```

ちょっと特殊な関数です。上記のようにすると、`1`, `3`, `5`, `7`の4つの値の中から、`1`が出る確率が一番高く、次に`3`, その次に`5`, 最も出にくいのが`7`という条件で値を選んで返します。

### Unityとの違い

Phaserの乱数では、整数は最大値を含み、実数は最大値を含まないという傾向があります。一方、Unityは逆で、整数の乱数は最大値を含まず、実数は含みます。乱数は、求め方や求める範囲が言語やライブラリごとに相当違いがありますので、使うときは気をつけてください。

## 演習：乱数で出現場所と移動速度を求める

乱数を使ってみます。まずはキャラクターの出現場所が毎回変わるようにしましょう。出現範囲は以下の通りです。

![](/assets/2017-06-27_18h19_55.png)

横方向は、80ピクセル～560ピクセルまで。縦方向は80ピクセル～280ピクセルまでです。座標には小数点は不要ですので、整数で求めます。`integerInRange()`を使いましょう。では、`create.js`を開いて、星の出現コードを以下のように書き換えます。

```javascript
    star = game.add.sprite(game.rnd.integerInRange(80,560), game.rnd.integerInRange(80, 280), 'star');
```

同様に、星のX,Y双方の初速を`-200`～`200`に設定しましょう。以下のようなコードを`create()`関数内の最後に追加します。

```javascript
    star.body.velocity.x = game.rnd.realInRange(-200, 200);
    star.body.velocity.y = game.rnd.realInRange(-200, 200);
```

こちらは`realInRange()`を使って実数で求めました。`200`は含まれませんが、`199.9999`と`200`の違いを感じることはありません。気にしなくて構わないでしょう。

以上できたら、保存をして、Webブラウザーでハード再読み込みをして、動作を確認してください。毎回、画面に表示される範囲と、速度が変化します。

## 考察：じゃんけんの乱数

じゃんけんを作るには、一般的には0～2の乱数を求めて、0をグー、1をチョキ、2をパーのように、数値と手を対応付けて表現します。その場合は以下のように乱数を生成して、変数`te`に代入したのがコンピューターの手として、プレイヤーの手と比較して勝敗を決めます。

```javascript
let te = game.rnd.integerInRange(0,2);
```

Phaserでは、pickを使うことで面白い作り方ができます。

```javascript
let te = game.rnd.pick(["グー", "チョキ", "パー"]);
```

以上で、変数`te`に、文字列で`グー`、`チョキ`、`パー`が得られます。それを数値に変換するか、直接文字列で判定して作ることができます。

## まとめ

ゲームを作る際、プレイヤーが予想できない要素として、乱数を取り入れることが多いです。Phaserには多くの乱数を求める機能が用意されていて、その代表的なものである`game.rnd.integerInRange()`と`game.rnd.realInRange()`を使ってみました。これにより、星の出現位置と動く速度を、実行するたびに変化させることができるようになりました。

