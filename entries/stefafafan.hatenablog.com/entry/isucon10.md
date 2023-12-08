---
Title: RustでISUCON 10に挑戦したが予選敗退した
Category:
- 技術
Date: 2020-09-13T15:27:24+09:00
URL: https://blog.stenyan.jp/entry/isucon10
EditURL: https://blog.hatena.ne.jp/stefafafan/stefafafan.hatenablog.com/atom/entry/26006613627288135
---

ISUCON 10 というWebアプリケーションを限られた時間で高速化するコンテストに参加してきました。「Trust Rust」チームということで id:dekokun:detail と id:Krouton:detail と参加しましたが残念ながら予選敗退しました。

まだ最終スコアや順位まではわからないが、我々が最後に提出したタイミングでは1133点でした。後ほどこのあたりにまとめられそう:
[http://isucon.net/archives/54704557.html:embed:cite]

[:contents]

** 準備編
過去にもISUCON参加したことがあり、今回もISUCON開催されるらしいぞと社内で盛り上がっていたので、よっしゃ僕もと参加を決意しました。

*** チームビルディング
社内ですでに dekokun と Krouton がRustで参加する気満々っぽくて、チームメンバーを募集していた。彼らとは普段仕事では別のチームですが、自分もRustにはちょうど興味はあったのでチームになった。

同じ会社ということで、週1でランチタイムに予定を抑えてRustでISUCONに挑戦するにあたっての準備をどうすすめるかの会を行うことにした。社内のScrapbox上で今週やったこと、来週までにやることを決めるというイメージ。最初は「初期実装なしでもRustでがんばるぞ！」という意気込みで進めていた。

><figure class="figure-image figure-image-fotolife" title="Trust Rust Scrapbox">[f:id:stefafafan:20200913142040p:plain]<figcaption>Trust RustチームのScrapbox</figcaption></figure><

*** ISUCONに必要そうな要素
Scrapbox上でどういう要素の勉強をしておくべきか？というのを書き出して、各々準備を進めていました。

- Rustの勉強
-- [https://tourofrust.com/:title] で勉強
-- Future 周りの勉強
-- profiler ([https://github.com/svenstaro/cargo-profiler:title=svenstaro/cargo-profiler], [https://github.com/flamegraph-rs/flamegraph:title=flamegraph-rs/flamegraph]とか)
- Rust + Webアプリケーションで必要になりそうなライブラリなど調べて勉強
-- web framework: [https://github.com/actix/actix-web:title=actix/actix-web]
-- mysql / postgresql: [https://github.com/launchbadge/sqlx:title=launchbadge/sqlx]
-- serialization: [https://github.com/serde-rs/serde:title=serde-rs/serde]
-- template engine: [https://github.com/Keats/tera:title=Keats/tera]
-- HTTP client: [https://github.com/seanmonstar/reqwest:title=seanmonstar/reqwest]
- Rust関係なしに、ISUCONっぽい話題
-- DBのテーブルにINDEX貼ったりする
-- nginxから静的ファイルを返す
-- mysql ログ解析(遅いクエリの特定): slow query log + [https://www.percona.com/doc/percona-toolkit/LATEST/pt-query-digest.html:title=pt-query-digest]
-- nginxログ解析(遅いエンドポイントの特定): [https://github.com/tkuchiki/alp:title=tkuchiki/alp]
-- [https://mackerel.io/ja/:title=Mackerel], [https://newrelic.co.jp/:title=New Relic]
-- デプロイ

dekokunはインフラ周り強くて、KroutonはRustに詳しい、僕はRustは初心者だけどアプリケーションエンジニアではあるという感じだったので、結局分担としてはこういう感じになりました。

- dekokun: デプロイや手元環境周りの整備の準備と、sqlx や actix-web の勉強
- Krouton: 他メンバーよりRustに詳しいのでそもそものどのライブラリが使えそうかというコメントをしてくれたり、Future周りとか、actix-webやsqlxなどの細かい挙動など調べる
- stefafafan: まずRustの勉強をしてから、profilerについて調べたり、pt-query-digestやalpの使い方になれたりMackerel周りだったり

*** ISUCONリハーサル

実際に3人集まって8時間使って祝日に ISUCON のリハーサルと称して練習しました。使ったのはISHOCON2というISUCONリスペクトなアプリケーションです。これ使えば自分たちだけでもひとまず練習はできそうということで選びました。

[https://github.com/showwin/ISHOCON2:embed:cite]

dekokunも以前ブログに書いていた。
[https://dekotech.dekokun.info/entry/2020/08/23/193513:embed:cite]

リハーサルはこういう流れで進んだ。

- dekokun が環境をローカルでDocker使って動かせるように準備
-- すぐに動いたわけではないのでこの間に Krouton はサーバ上で直接コード書き換えて動かしたりしていた
-- この間 stefafafan はサーバ上のコードをgit管理させたり、nginx.conf や my.cnf にシンボリックリンクを貼って一緒にgit管理できるようにしていた
- また、stefafafan は alp や pt-query-digest を使ってなんとか解析をやった。mackerel-agent と mackerel-agent-plugins も入れた
- Krouton がなんとか既存の他言語の実装をみながら、アプリケーションコードの /vote のエンドポイントを Rust + actix-web + sqlx + tera で書き換える
- あとからきた dekokun / stefafafan で Krouton の実装を参考に他エンドポイントも Rust + actix-web + sqlx + tera で書き換えていく
- 結局アプリケーション側の高速化以前に、Rustへの書き換えだけで時間切れになったし書き換えは終わらなかった

リハーサルでわかったことは色々あって、

- Rust初回コンパイルは時間かかって大変
- mysql の my.cnf 周りは AppArmor による妨害などでハマりがち
- SQLやテンプレートをひたすら移植しているだけでつらい
-- GoのテンプレートをRustのテンプレートに置き換えるのは人間が手でやる仕事ではなさそうという気持ちになった
- rust-analyzerは便利なのでVSCodeで入れておこう
- フレームワークの素振りが足りなかった

などなど。結局、Rustの初期実装なしに参加しても、移植作業をひたすらやるだけになりそうでつらいので、初期実装ほしいね、と会話した。

[https://twitter.com/dekokun/status/1292758403345862656:embed]

最終的にRustの参考実装ありになってとても助かりました。 :pray:

** 予選当日

大体リハーサルと同じ流れで作業を進められたので、リハーサルはやってよかったなと感じた。最初に全員でREADME読んで全員でWebアプリケーション触って雰囲気掴んだのもよかった。物件+椅子のサイトだった。ISUCONだけに :thinking_face:

- dekokunがデプロイスクリプトや解析を手元から叩くためのスクリプト準備
- stefafafanがalp/pt-query-digest/mackerel系の準備
- Kroutonが一足先に実装を読んで怪しいところに手を入れていった

一度alp/pt-query-digestの解析結果を出した後に、dekokun/stefafafan2人でちょっと見て、searchが遅そうとか、nazotte検索が遅そうみたいな話をして、Kroutonにさらに関連コードをみてもらいつつ、INDEX貼ったらスローログは解消しないかなとかやってました。

最初から最後までこの感じで進めていったわけではなく、途中stefafafanがコードをみてdekokun/KroutonでINDEXの様子をみるとか、ペアを入れ替えながらペアプロできて結構よかった気がする。

（参考実装がなかったら、怪しいエンドポイントみつけたとしてもまずはRustで再実装するというステップが待っていたと思うと、最初からRustの実装があるありがたみがすごかった）

ここまで書いてて、実装言語がなんであれ結局遅いエンドポイントや遅いクエリを見つけては改善するの繰り返しなのでこのへんのツールに慣れておくのは大事だなと思った。

最後のほう、DBのchairテーブルとestateテーブルを同時に参照することは基本ないということで、dekokunがDBを分離するぞといって進めていたけど、actix-webで複数のDB Poolを扱うにはどうすると良いのだろうとなって、自分が軽く調べて勘で「actix-webのweb::Data周りは自前の型を作って渡す感じでもいけるのでは？」と案をだしてみて実際それを試したら上手くいったのは少し嬉しかった。

一方で、「SQLの結果をRedisにキャッシュする前段階として、ひとまずアプリケーション側でキャッシュさせる」という作戦については、dekokunが軽く試した結果所有権周りの問題になったので、Kroutonにヘルプを求めて、mutexを使った結果コンパイルエラーは解消したものの、ベンチマーク通したら不正扱いになったので改善として提出できずに終わってしまった。ちょっと惜しい。

** 総括
- Rust自体や、Rustの関連ライブラリについて調べて触る機会になってよかった
- 以前自分が参加したISUCONと比べては、わりと分担などはうまくいった気がする、予選突破できなかったのは残念だけどいくつかの改善を入れることはできたのでまあOKという感じ
- 下準備を素早く終わらせられるようにするのは、ISUCON的には慣れておくべきっぽい
- Trust RustということでRustを信じてやっていたものの、Rustの強みを活かせることができたかどうかは不明。コンパイルが通ったときの安心感はちょっとあったかもしれない
- 参考実装なしの言語で挑戦する場合は同じ時間制限ないに1から同じコードを再実装する、みたいなタスクがあって大変
- ISUCONの問題自体が結構面白くてよかったので運営ありがとうというのと、また次回あれば参加したい
