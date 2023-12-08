---
Title: Mackerelが公開しているOSSにPull RequestしてOSS慣れしてみる
Category:
- 技術
Date: 2020-12-13T16:57:15+09:00
URL: https://blog.stenyan.jp/entry/mackerel-oss
EditURL: https://blog.hatena.ne.jp/stefafafan/stefafafan.hatenablog.com/atom/entry/26006613664165272
---

こんにちは、株式会社はてなで最近はマンガサービスのコードを書いている id:stefafafan:detail です。この記事は[https://qiita.com/advent-calendar/2020/mackerel:title=Mackerel Advent Calendar 2020]の13日目の記事です。

[https://qiita.com/advent-calendar/2020/mackerel:embed:cite]

今回はMackerelのOSSにPull Request送ったときの話をしてみます。
====

** 前提
一応簡単に前提から話すと、Mackerelというのは株式会社はてなが開発しているサーバ監視サービスです
[http://mackerel.io/:embed:cite]

Webサービス本体のコード自体は公開されていないですが、監視対象のサーバにインストールされるエージェントやプラグイン、また公式ドキュメントのマークダウンなどは大体GitHubにオープンに公開されています。プラグインなどはAPIが公開されているので誰でも簡単に作れます。

[https://github.com/mackerelio:embed:cite]

今回はこの公開されているコードに対して誰でも気軽にPull Request送れますよという話をします。

** いくつかのリポジトリの簡単な紹介
本題に入る前に、mackerelioで公開されているリポジトリは40件以上あるので、今回自分が触ったものを中心にいくつかのリポジトリをそもそもどういうものなのか簡単に紹介します。

*** mackerelio/documents
[https://github.com/mackerelio/documents:embed:cite]
- ここには公式ドキュメントとして公開されるもととなるマークダウンのファイルが置かれています。
- [https://mackerel.io/ja/docs/] あたりに公開されている文章などで「おや？」と思ったものがあれば上記リポジトリにPull Requestすると取り込んでもらえます。

*** mackerelio/mackerel-client-go
[https://github.com/mackerelio/mackerel-client-go:embed:cite]
- mackerel-client-go は Mackerel の API を叩くGo言語のクライアントです。
- [https://mackerel.io/ja/api-docs/] に載ってるAPIを利用したツールなどをGoで作りたいときに使うと良いでしょう。

*** mackerelio/mkr
[https://github.com/mackerelio/mkr:embed:cite]
- mkr は Mackerel の API を叩くGo製のCLIツールです。
- 内部では mackerel-client-go を使っています。 jq コマンドと一緒に使うと便利です。

*** mackerelio/mackerel-agent
[https://github.com/mackerelio/mackerel-agent:embed:cite]
- mackerel-agent は名前の通りサーバにインストールするエージェントのリポジトリです。こちらもGo製。
- インストールされたサーバの基本的なシステムメトリクスを収集し、MackerelのWebサーバに送ってくれます。

*** mackerelio/mackerel-agent-plugins
[https://github.com/mackerelio/mackerel-agent-plugins:embed:cite]
- mackerel-agent-plugins は公式でまとめてる mackerel-agent 向けのプラグイン集です。
- 例えばnginxが動いてる場合はnginx周りのメトリクスを追加で取得したいからこれに含まれてるmackerel-plugin-nginxを有効化するなど、mackerel-agent単体で取れるメトリクスよりも追加で色々取りたい場合に使います。

*** mackerelio/go-mackerel-plugin
[https://github.com/mackerelio/go-mackerel-plugin:embed:cite]
- go-mackerel-plugin はGo言語でMackerelエージェント向けのプラグインを新規で作る際に利用できる便利なプログラムです。
- mackerel-agent-pluginはどれも「グラフ定義を作る」「値を取得する」を必ずやることになるので、これを使うと同じフォーマットで簡単にそれが実装できるというもの。
- [https://github.com/mackerelio/go-mackerel-plugin-helper:title=mackerelio/go-mackerel-plugin-helper] という似た名前のリポジトリもありますが、READMEいわくgo-mackerel-pluginを使うのが推奨されているらしい。

** 以前自分がPull Request送ったときの流れ
自分は普段からGoをバリバリ書いてるわけでもMackerelのOSSをバリバリ使い込んでるまではいかなかったので、実はOSSにPull Requestするのは少しハードルが高かったのですが、英語は得意なのでドキュメントを読んで知識を得つつタイポや文法ミスなどあればついでに直していくところから始める作戦でいきました。

*** まずはドキュメントに対するタイポミスなど簡単なところからPull Request
[https://github.com/mackerelio/documents/pull/4:embed:cite]

上記でやったこととしては、
- ドキュメントの簡単なタイポミス修正
- 英語の文法を改善したり、表記を統一したりした
- Pull Requestにする際はdescriptionに簡単に英語でやったことを書いた

レビューは比較的すぐに英語で返してもらえる。英語だけどはてなの人が基本返してくれてるので、知らない海外の人とやりとりするよりはハードルが低いかもしれない（自分の場合は同僚なのでその分さらにハードルは低かったです）。

これや他にも送ったdocumentsへのPull Requestはすべて取り込んでもらい、実際に公開されてるドキュメントにも反映してもらえました。

*** 慣れてきたらAPIクライアント周りのコードを読んだり触ってみたりしていく
APIドキュメントを読んで雰囲気がわかってきたので、実際にmackerel-client-goのコードを手元にgit cloneし、中身を読んだりgoのテストを実行してみたりとしてました。その中でファイル名にタイポを見つけたので早速Pull Requestしました。

[https://github.com/mackerelio/mackerel-client-go/pull/100:embed:cite]

もっとコードとドキュメントを読んでるうちに、「APIはあるがmackerel-client-goには実装されていない」ものを見つけたので、通知チャンネル系のAPIに対応させちゃおうということでここでようやくコードを書いてみました。

[https://github.com/mackerelio/mackerel-client-go/pull/101:embed:cite]

普段業務でMackerelチームでGo言語を書いているエンジニアの方にレビューしてもらえるのでありがたいです。今回の変更ではGoのomitemptyの仕様についてなんとなく理解が深まったというメリットもありました。

[https://github.com/mackerelio/mackerel-client-go/pull/101#issuecomment-574652286:embed:cite]

*** APIクライアントを利用している他のOSSの様子を見ていく
mackerel-client-goがチャンネル系のAPIに対応していなかったということは、CLIツールであるmkrもチャンネル周りに対応していないということではないかということで次はこのリポジトリをgit cloneして中身を見ました。結果以下のPull Requestを作ることができた。

[https://github.com/mackerelio/mkr/pull/270:embed:cite]

やったこととしては、
- そもそもimportしてるmackerel-client-goのバージョンをあげないとせっかく対応したchannel周りの実装がこっちでは使えないのであげた
- mkr monitorsというサブコマンドの実装を参考にmkr channelsサブコマンドを実装してみた
- Makefileを参照し、手元でビルドを作成して試しに実行する方法を見つけてそれで試した (make crossでMac向けのビルド作れそうだったのでそれでやった)

CLIツールの新しいサブコマンドを作るということで、全体的な設計がどうなっているかや、Goでテストを書くときの雰囲気などが少し味わえて良い体験でした。

*** 無事取り込まれたら公式のブログにも取り上げてもらえた
mkr channels pullが実装したということで以下のブログにも取り上げてもらえました。

>https://mackerel.io/ja/blog/entry/weekly/20200414:title>
- mkr channels pull が利用可能になりました
-- これを実行すると、ローカルに channels.json ファイルが作成され、利用している API キーに対応するオーガニゼーション設定されているチャンネル情報が出力されます。
-- 出力される内容は、Mackerel の API で取得可能な項目となります。
<<

[https://mackerel.io/ja/blog/entry/weekly/20200414:embed:cite]

** まとめ
今回はMackerelの公開しているOSS群を一部簡単に紹介したとともに、自分がPull Requestを送ったときの例を紹介しました。同じ日本人が英語でレビューしてくれるし、触ってるうちに何となく全容がわかってくるのでオススメです。もちろんMackerelだけでなく、自分が普段使ってるツールなどは何でも公開されているOSSがあれば、Pull Request送らないにしてもコードの雰囲気を眺めるだけでもやれると発見があって良いかもしれません。
