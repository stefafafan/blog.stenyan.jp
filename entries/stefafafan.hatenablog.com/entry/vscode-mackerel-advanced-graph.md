---
Title: Mackerelの「式グラフ」記法のシンタックスハイライトに対応したVS Code拡張を作った
Category:
- 技術
Date: 2021-12-12T21:00:45+09:00
URL: https://blog.stenyan.jp/entry/vscode-mackerel-advanced-graph
EditURL: https://blog.hatena.ne.jp/stefafafan/stefafafan.hatenablog.com/atom/entry/13574176438042100647
---

こちらは[https://qiita.com/advent-calendar/2021/mackerel:title=Mackerel Advent Calendar 2021]の12日目の記事です。

先日以下のような記事を書いたのですが、Mackerel Advent Calendarを今日みたら空きがあったのでついでにMackerel向けの拡張もさっと作ってみました。
（VS Codeの拡張作りに関する話は以下のエントリに色々書いてるので興味あればこちらもよろしくお願いします）。

[https://stefafafan.hatenablog.com/entry/vscode-hatena:embed:cite]

** 「式グラフ」とは

Mackerelでは実験的機能として、式を書いて既存のメトリックの値を組み合わせて、グラフを作ることができます。

[https://mackerel.io/ja/docs/entry/advanced/advanced-graph:embed:cite]

また、同じ「式」を利用して、監視も行うことができます。

[https://mackerel.io/ja/docs/entry/expression-monitoring:embed:cite]

おすすめな使い方などについてはこのエントリが参考になると思います。

[https://mackerel.io/ja/blog/entry/2020/12/12/000000:embed:cite]

** 式を書くのがちょっと難しい

上記の式ですが、Mackerel特有のものなので手元のエディタなどで定義を書いていくのが意外と難しい。そう思ったので、VS Codeの拡張で関数名などに色をつける拡張作ってみました。

** 作りました
よろしくお願いします。

[https://marketplace.visualstudio.com/items?itemName=stefafafan.mackerel-expression-graph-syntax:embed:cite]

リポジトリです。
[https://github.com/stefafafan/vscode-mackerel-expression-graph-syntax:embed:cite]

この拡張を入れて言語モードを <code>Mackerel</code> にすると (もしくはファイルの拡張子を <code>.mackerel</code> にすると) 以下のように関数名や一部のシステムメトリックなどに色がついてみやすくなります。色の付き具合はお使いのテーマにもよりますので色々試してみてください。

><figure class="figure-image figure-image-fotolife" title="拡張を入れた様子">[f:id:stefafafan:20211212204018p:plain]<figcaption>拡張を入れた様子</figcaption></figure><

括弧の対応をわかりやすくする拡張や、インデントに色をつける拡張などと併用するとよりわかりやすさが増して良いと思います。

><figure class="figure-image figure-image-fotolife" title="ほかの拡張と併用した状態">[f:id:stefafafan:20211212204711p:plain]<figcaption>ほかの拡張と併用した状態</figcaption></figure><

[https://marketplace.visualstudio.com/items?itemName=2gua.rainbow-brackets:embed:cite]
[https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow:embed:cite]

ぜひご活用ください。
