---
Title: VSCode+Extemporeで音を鳴らしたいよね
Category:
- 技術
Date: 2018-12-05T00:00:00+09:00
URL: https://blog.stenyan.jp/entry/extempore
EditURL: https://blog.hatena.ne.jp/stefafafan/stefafafan.hatenablog.com/atom/entry/10257846132680142681
---

こんにちは〜。こちらは、 [https://qiita.com/advent-calendar/2018/hatena:title=はてなエンジニア Advent Calendar 2018] 5日目の記事です。
[https://qiita.com/advent-calendar/2018/hatena:embed:cite]

昨日は id:yutailang0119:detail さんの [http://developer.hatenastaff.com/entry/2018/12/04/190053:title=モバイルアプリエンジニア向け、便利CLIツール作成のススメ] でした。

====

** 音楽作りたい

突然ですが、音楽が作りたいと最近思い始めています。どうやって音楽を作るんでしょうねとググったらAudio Programming Languageというのを知りました。

[https://www.google.co.jp/search?q=audio+programming+language:title=ググる]

** なるほど、これ使えば僕も曲が作れそう

[https://en.wikipedia.org/wiki/List_of_audio_programming_languages:title=色々あるみたいだけど]、今回はExtemporeっていう言語を触ってみようと思います。
[https://extemporelang.github.io/:embed:cite]

オライリー公式で動画出てるけどなんかすごそうですね。リアルタイムで音を出せて便利そう
[https://www.youtube.com/watch?v=yY1FSsUV-8c:embed:cite]

** 環境セットアップ

インストール方法については[https://extemporelang.github.io/docs/overview/install/:title=ここに書いてある]、英語しかなさそうだけど、自分でいじってビルドとかしない限り普通にバイナリ落とせば良さそう
[https://extemporelang.github.io/docs/overview/install/:embed:cite]

[https://extemporelang.github.io/docs/overview/editor-support/:title=エディタもいくつか対応している]が、ぱっと見VSCode推しのようだし、僕も最近VSCodeで仕事しているのでそれをいれるのが楽そうですね。

<figure class="figure-image figure-image-fotolife" title="I see..">[f:id:stefafafan:20181204204245p:plain]<figcaption>I see..</figcaption></figure>

VSCodeのMarketplaceでExtemporeで調べるといいやつがでてくるのでそれを入れれば準備は完了。
<figure class="figure-image figure-image-fotolife" title="Parinferも便利そう">[f:id:stefafafan:20181204204526p:plain]<figcaption>Parinferも便利そう</figcaption></figure>

** サンプルプログラムを動かしてみる
+ Extemporeを実行する
+ サンプルプログラムを開く
+ Extempore Connectで接続する
+ 該当の行を実行したりする
+ 音がなるので適当に数字をいじってみて遊ぶ

というのを以下の動画でやってみました。みんなも参考にやってみて (※1:53辺りから初めて音が鳴ります）
ちなみに以下では実行したい行はコマンド+Enterで実行しています。（ショートカット自体は好みのやつに変更できそう）

[https://www.youtube.com/watch?v=2XIED9pwghc:embed:cite]

** ドキュメントを参考にちょっとずつコードを理解したい

何もわからないまま音を出しているのでドキュメントを見ます
[https://extemporelang.github.io/docs/guides/note-level-music/:embed:cite]

ドキュメントの通りだと実は上手く動かなかったので以下のように変更して実行した
>|diff|
;; instrumentファイルをロードする
(sys:load "libs/core/instruments.xtm")

;; シンセの定義
-- (make-instrument synth fmsynth)
++ (bind-instrument fmsynth fmsynth_note_c fmsynth_fx)

;; aDSP output クロージャにシンセを追加する
(bind-func dsp:DSP
  (lambda (in time chan dat)
--    (synth in time chan dat)))
++    (fmsynth in time chan dat)))
(dsp:set! dsp)

;; シンセを使って音を鳴らす
--(play-note (now) synth (random 60 80) 80 (* 1.0 *second*))
++(play-note (now) fmsynth (random 60 80) 80 (* 1.0 *second*))
||<

（良い機会なのでドキュメントのコードについてはissue立てました）
[https://github.com/digego/extempore/issues/347:embed:cite]

なるほどこれだけ見ると結構シンプルですね。特に下の行の数字をいじるとわかりますが、最初の60 80はrandomで60-80の数字を選びそれが音程となる、その次の80が音量、最後の1.0は音を鳴らす秒数というのが何となくわかります。
>|lisp|
(play-note (now) fmsynth (random 60 80) 80 (* 1.0 *second*))
||<

上記を応用して関数内で複数の音を鳴らすというのも出来るみたいでこれも意外と直感的かもしれない。
>|lisp|
(define chord
   (lambda ()
      (play-note (now) fmsynth 60 80 *second*)
      (play-note (now) fmsynth 64 80 *second*)
      (play-note (now) fmsynth 67 80 *second*)))

(chord)
||<

で何よりせっかくSchemeベースなので、再帰的なコードとか書いて行こうぜ、みたいなことがドキュメントに書いてありますが、そろそろアドベントカレンダー記事投稿時間が迫ってきたので今日は一旦この辺で。音さえ出せるようになればあとは遊んでれば何とかいい感じになるでしょうと期待したい…

明日のはてなエンジニアアドベントカレンダーの担当は id:susisu:detail です！
