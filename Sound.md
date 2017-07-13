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
万能のフォーマットはありません。WAVEは容量が大きいため非推奨です。そこで、`MP3`と`OGG Vorbis`の2つのデータを用意するのがよいでしょう。Phaserでは、以下のようにすることで、指定した2つのファイルのうち、使える方を読み込んでくれます。

```javascript
game.load.audio('BGM', ['assets/audio/bgm.mp3', 'assets/audio/bgm.ogg']);
```

## フォーマット変換
http://media.io/ で、様々なフォーマットに変換できます。MP3、OGG、WAVEのどれか1つしか手元にない場合は、このサービスで足りないフォーマットのデータに変換するとよいでしょう。

## 参考URL
- https://developer.mozilla.org/ja/docs/Web/HTML/Supported_media_formats

# BGMの再生

https://phasergames.com/phaser-audio-delay/

# SEの再生

