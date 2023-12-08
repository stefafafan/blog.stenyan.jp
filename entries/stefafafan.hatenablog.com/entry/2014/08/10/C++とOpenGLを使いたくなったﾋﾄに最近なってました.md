---
Title: C++とOpenGLを使いたくなったﾋﾄに最近なってました
Category:
- 技術
Date: 2014-08-10T21:41:08+09:00
URL: https://blog.stenyan.jp/entry/2014/08/10/C%2B%2B%E3%81%A8OpenGL%E3%82%92%E4%BD%BF%E3%81%84%E3%81%9F%E3%81%8F%E3%81%AA%E3%81%A3%E3%81%9F%EF%BE%8B%EF%BE%84%E3%81%AB%E6%9C%80%E8%BF%91%E3%81%AA%E3%81%A3%E3%81%A6%E3%81%BE%E3%81%97%E3%81%9F
EditURL: https://blog.hatena.ne.jp/stefafafan/stefafafan.hatenablog.com/atom/entry/12921228815729985667
---

#### Effective Modern C++
まずご存知の方もいると思いますが、Scott Meyers氏が書いたEffective Modern C++のEarly Release版がオライリーで発売されてます最近。Amazonなどでは予約受付中のようですね。


<!-- more -->


[asin:1491903996:detail]

僕実はみんなから評判の高いEffective C++っていう本を一度も読んだこと無いし買ってもいないんですよね。それで買おうか買おうかずっと思ってるところでScottさんがC++11とC++14用に書いてると聞いて結局こっちを買いました。今とりあえず90ページ辺りまで読み終えましたけどこれはかなり良さそう、C++11やC++14対応の本をあまり読んでいなかったのもあると思いますが、しっかりとautoとかdecltypeとかnullptrとかの利点が載ってて、あ、これいいなって思いました。C++14とかを使いこなせるように絶対なりたいのでこの本はしっかりと読んでおきたいです。絶対オススメ。

#### OpenGL
で。C++14の事とか読むのもいいけども。実際にプログラミングで使いたいよね！ってことになって、あぁ、学校でOpenGLを使った課題やってたとき楽しかったなぁって思ってOpenGLやろうと思ったマンです。

そこでこのサイトです：http://open.gl/　もうURLの通り、OpenGLのことについて書いてあります。このサイトはチュートリアルで、わりと最近の仕様で書いてあるので良いと思います。（OpenGLについてググっているとImmediate modeといってglBeginとかのようなdeprecatedな関数を使ったチュートリアルがいっぱいヒットするので困るのです）。とりあえずこのサイトにそってやってれば結構いいかな？って思ったのです。昨夜。

##### SFML
SFML導入までの道のり

* Visual Studio 2013 で使うために [http://www.sfml-dev.org/download/sfml/2.1/:title] からとりあえずVisual C++ 11 (2012) - 64 bitsをダウンロード、[http://www.sfml-dev.org/tutorials/2.1/start-vc.php:title] を参考にインストールしようとするが色んなエラーがでてくる（リンカーとか32 bitがどうのとか複数のエラー）

* なんかVSの環境色々するのだるいしとりあえずVMwareにUbuntu入れた→せっかくなのでclang使いたいな～→-std=c++1yでコンパイルしようとすると以下のエラー：

>  no member named 'gets' in the global namespace
  using ::gets;
        ~~^
1 error generated.

* 上のエラーに関しては江添さんのブログで書いてあった：[http://cpplover.blogspot.jp/2013/11/clang-stdc1y.html:title]、それでこのことに関してツイートしていたら江添さん本人から突然のリプライ：

[https://twitter.com/EzoeRyou/status/498271698622242817:embed#@stefafafan @aoisensi Ubuntu 14.04のClangなら治っているはず。]


* 一応バージョン確認してみるけどUbuntu 14.04 LTSだった。[http://stackoverflow.com/questions/17775390/clang-3-3-in-c1y-mode-cannot-parse-cstdio-header:title]にて同じような問題に直面してる人が居たけど数名がglibcのダウングレードで解決したらしい。でも僕は妥協してとりあえずgcc使おうってことになった。

* 実際のSFMLやGLEWはLinuxからだとわりと簡単に導入できました。基本sudo apt-get install hogehoge で行ける感じ。最初からVisual Studioなんか試さずLinuxでやればよかった。

* せっかくなので[http://moebuntu.web.fc2.com/:title]を導入して可愛くしました。イマココ


っていう流れを昨晩ずっとやってたら気がついたら朝5時くらいになってました。とほほ。
ところでC++とOpenGLで何を作るんだいって？何も考えてませんよそんなのただプログラミングしたいだけです。Apple Swiftも良いけどもC++14が一番和みますねやっぱり。


とりあえず以上です、おやすみ。




