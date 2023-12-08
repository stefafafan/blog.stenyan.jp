---
Title: iTerm2の機能を使いこなして日頃の作業の効率をあげたい2020
Category:
- 技術
Date: 2020-12-06T11:00:00+09:00
URL: https://blog.stenyan.jp/entry/iterm2
EditURL: https://blog.hatena.ne.jp/stefafafan/stefafafan.hatenablog.com/atom/entry/26006613660783496
---

この記事ははてなエンジニア Advent Calendar 2020の6日目の記事です。
[https://qiita.com/advent-calendar/2020/hatena:embed:cite]

5日目は id:mizdra:detail さんによる[https://www.mizdra.net/entry/2020/12/05/234806:title=polyfill を深堀りする]でした。フロントエンド周りは弱い自分としてもこの記事を読むだけでpolyfillの概要から今後どうすると良いかなど知れてとてもありがたい記事でした。

[https://www.mizdra.net/entry/2020/12/05/234806:embed:cite]

さて今日は視点変えてツールの話を書きます。毎日使っているツールこそ、提供されている便利な機能を把握しておきたい。Macで使ってるターミナルアプリ「iTerm2」について書きます。
[https://iterm2.com/:embed:cite]

まずはみんながひょっとしたら知らない基本操作についていくつか紹介し、後半はTriggersという機能の使い方をいくつか紹介します。
(記事執筆時点ではver.3.4を使っています)

====

** tl;dr

- 公式ドキュメントにいろいろ書いてあるので読みましょう。
- iTerm2使ってるならTriggerをいろいろ設定しましょう。

** iTerm2で知ってるとちょっと便利な基本操作
*** 検索は正規表現にも対応してます
- Command+Fとかでターミナル内検索ができるのはご存知の方多そうですが、そのときに表示される下矢印をクリックすると検索オプションを選べます。大文字小文字を区別するかどうか、正規表現で検索するかどうかなどが選べます。
><figure class="figure-image figure-image-fotolife" title="大文字小文字区別せずに検索している様子">[f:id:stefafafan:20201205182544p:plain]<figcaption>大文字小文字区別せずに検索している様子</figcaption></figure><
><figure class="figure-image figure-image-fotolife" title="正規表現を使って検索している様子">[f:id:stefafafan:20201205182640p:plain]<figcaption>正規表現を使って検索している様子</figcaption></figure><

*** iTerm2に貼り付けしたものはPaste Historyとして履歴を一覧してみたり絞り込みして選択できる
- iTerm2に何度もコピペしてるうちに「3つ前、4つ前に貼り付けしたものを再び参照したいな」となった場合はCommand+Shift+Hで履歴一覧がでます。ここからさらに文字を入力して絞り込むことも可能です。
><figure class="figure-image figure-image-fotolife" title="コマンドの途中で貼り付け履歴を呼び出している様子">[f:id:stefafafan:20201205183443p:plain]<figcaption>コマンドの途中で貼り付け履歴を呼び出している様子</figcaption></figure><
><figure class="figure-image figure-image-fotolife" title="貼り付け後">[f:id:stefafafan:20201205183519p:plain]<figcaption>貼り付け後</figcaption></figure><

- 実行履歴を元に絞り込み…みたいなのはpeco+zshとかでいけますが、Paste Historyは実行履歴ではなくあくまでも貼り付け履歴なので例えば文章やURLをコピペしたのをなんども参照するみたいな使い方ができます。

*** Toolbeltで実行履歴やペースト履歴を表示・選択
- Command+Shift+BでToolbeltの表示をトグルできます。
- Toolbeltメニューからここに何を表示するか項目をいろいろ選択できるので、先ほど言ってたPaste Historyだったり、Command Historyだったりを右側に常時表示しておいてそこからどれか選んで実行するみたいなこともできます
><figure class="figure-image figure-image-fotolife" title="実行履歴と貼り付け履歴を表示している">[f:id:stefafafan:20201205183556p:plain]<figcaption>実行履歴と貼り付け履歴を表示している</figcaption></figure><

*** Copy Modeに入ってキーボードのみでiTerm2上の文章をコピーする
- 出力結果などをコピーしたいとなった場合にマウスで選択してコピーもできますが、Copy Modeに入ってキーボード操作で選択してコピーもできます。
- Cmd+Shift+Cでコピーモードに入り、hjkl (もしくは矢印キー)で移動し、vで選択開始、yでコピーできます。
><figure class="figure-image figure-image-fotolife" title="Lorem ipsumの一部だけをCopy Modeを駆使して選択中">[f:id:stefafafan:20201205183937p:plain]<figcaption>Lorem ipsumの一部だけをCopy Modeを駆使して選択中</figcaption></figure><

*** Save Markで目印をつけて、キーボードショートカットでその目印をつけた場所へ1発で戻る
- 例えばコマンドの出力が大量にあって、そのコマンドを実行したところまで戻りたいケースなどは、マウスでたくさんスクロールしたりCtrl+Fで検索して戻るのがちょっと面倒なことがあるかもしれません。
- あらかじめ戻りたい場所が決まっている場合は、 Command+Shift+M でSave Markしましょう。チェックポイントをつけるイメージです。マークがついた行は青い印がつきます。
-- ちなみにShell Integrationをインストール済の場合はcommand promptごとに自動でマークされます。
><figure class="figure-image figure-image-fotolife" title="マークが設定されている行は左側に青い印がつく">[f:id:stefafafan:20201205184959p:plain]<figcaption>マークが設定されている行は左側に青い印がつく</figcaption></figure><

- あとでそのMarkに戻りたい場合は Command+Shift+J で Jump To Mark できます。一瞬でその部分へ飛ぶ上に行がハイライトされてわかりやすいです。もしくはMarkが複数ある場合は Command+Shift+矢印で上下に移動できます。
><figure class="figure-image figure-image-fotolife" title="Command+Shift+矢印で移動中の様子">[f:id:stefafafan:20201205185037p:plain]<figcaption>Command+Shift+矢印で移動中の様子</figcaption></figure><

*** Timestamp表示トグル
- Command+Shift+Eでタイムスタンプの表示をトグルできます。何時にどのコマンドを実行したのか、画面の横の方を見るだけでわかって便利です。
><figure class="figure-image figure-image-fotolife" title="Timestampが表示状態になっていると何時に何をやったかがわかる">[f:id:stefafafan:20201205185235p:plain]<figcaption>Timestampが表示状態になっていると何時に何をやったかがわかる</figcaption></figure><

*** さっきまで画面に表示されていたものを見たい場合はInstant Replayモードで時間を巻き戻す
- Instant Replayモードを使うと時間を巻き戻せます。
- Command+Option+Bでモードに入るのでここで矢印キーで左右押していくと秒単位で画面に表示されていたものが再び表示されます。
- 何時何分何秒に何が起きていたかこれであとで見返せるので便利です。
-- 試してみるとターミナルを縮めたとかも記録されていてすごい。
><figure class="figure-image figure-image-fotolife" title="Instant Replay Mode">[f:id:stefafafan:20201205190401p:plain]<figcaption>Instant Replay Modeのウィンドウが下に表示されている様子</figcaption></figure><

- デフォルトではこの機能のために4MB割り当てられています、もっと増やしたい場合は設定で増やせます。

** iTerm2で設定できる様々なTrigger紹介
ここから先はTriggers機能を使ってできることをいくつか紹介します。Preferences→Profiles→Advanced→Triggersで設定できます。
ここに紹介しているもの以外にもあります。使い方は工夫しましょう。
><figure class="figure-image figure-image-fotolife" title="Triggers設定画面はAdvancedの中にあります。">[f:id:stefafafan:20201205190636p:plain]<figcaption>Triggers設定画面はAdvancedの中にあります。</figcaption></figure><

*** 特定の文字列が表示されたらハイライトする Highlight Text
- ターミナルの出力のうち目立たせたい文字列があるならハイライトさせちゃいましょう。
- 自分は production という文字列が登場したら赤くハイライトさせたり、Compilation failedという文字列の場合は紫色にハイライトさせたりしてみてます。
-- 今回たまたま文字列をハイライトさせてますが、Macの通知を発火させるなども可能です。
><figure class="figure-image figure-image-fotolife" title="文字ハイライトのTrigger設定例">[f:id:stefafafan:20201205191352p:plain]<figcaption>文字ハイライトのTrigger設定例</figcaption></figure><
><figure class="figure-image figure-image-fotolife" title="productionという文字列が登場したら赤くハイライトされている様子">[f:id:stefafafan:20201205191309p:plain]<figcaption>productionという文字列が登場したら赤くハイライトされている様子</figcaption></figure><

*** Passwordを求められたときにiTerm2内臓のパスワードマネージャーを起動させる
- iTerm2にはPassword Managerがあり、ここにsudoしたときやsshしたときなどに聞かれるパスワードを入れておくことができます。
-- Window→Password Managerで開いてあらかじめパスワードを設定しておきましょう (キーチェーンに記録されます)。
><figure class="figure-image figure-image-fotolife" title="Password Manager設定画面">[f:id:stefafafan:20201205191908p:plain]<figcaption>Password Manager設定画面</figcaption></figure><

- 次にTriggerの設定でRegular Expressionのところに Password: を設定して、Actionに Open Password Manager、Instantもチェック入れておきましょう。
-- またパスワードが複数ある場合はデフォルトを設定できます。Parametersの欄に一番頻繁に聞かれるやつを設定しておこう。
><figure class="figure-image figure-image-fotolife" title="TriggerでPasswordという文字が登場したらPassword Managerが開かれるようにする設定">[f:id:stefafafan:20201205192043p:plain]<figcaption>TriggerでPasswordという文字が登場したらPassword Managerが開かれるようにする設定</figcaption></figure><

- これでPassword:というプロンプトが来るたびに自動的にパスワードマネージャーが開く。デフォルトのやつでOKならそのままEnter押せばOKだし、他のパスワード選びたかったら選択してEnterでOK。
-- ※ パスワードを使うとき、キーチェーンのロック解除を求められることがあるがTouch IDつきMacBook Proを使ってる場合はTouch IDで解除可能
><figure class="figure-image figure-image-fotolife" title="Passwordという文字が出てきたタイミングでPassword Managerが自動で呼び出された">[f:id:stefafafan:20201205192155p:plain]<figcaption>Passwordという文字が出てきたタイミングでPassword Managerが自動で呼び出された</figcaption></figure><

*** コンパイルエラーや実行結果のエラーをCaptured Outputでキャプチャし、Toolbeltの欄に一覧を出力する
- 使う言語に細かい差はありそうですが、実装中にコンパイルエラーが複数出力されたり、テストケースが複数失敗したりすることが日頃ある。
- このとき出力の量が多いとそれぞれの出力の最初の行がどこなのかを見つけるためにスクロールしつつ目でgrepするなりする必要があり、試行錯誤中だとこれが地味に面倒だったりする。
- Captured OutputのTrigger使うとこれがちょっとマシにできる。
- 自分の場合はPerlで最近開発をしていたので、Perlのテスト出力結果のうち「not ok 数字 - ...」出てる行から先を見つけたい。この場合はRegular Expressionのところに「not ok [0-9+]」と入れて、ActionはCapture Outputに設定する
><figure class="figure-image figure-image-fotolife" title="Perlの失敗したテストのために雑にTriggerを設定した">[f:id:stefafafan:20201205193000p:plain]<figcaption>Perlの失敗したテストのために雑にTriggerを設定した</figcaption></figure><

- あとはToolbeltのCaptured Outputを表示した状態でPerlの失敗するテストを実行するとリアルタイムに右側にこの行とこの行とこの行がマッチしたというのが一覧で表示される。それぞれクリックするとその行へとジャンプしてくれるので非常に便利。
><figure class="figure-image figure-image-fotolife" title="テストを実行すると右側に正規表現マッチした行がキャプチャされる">[f:id:stefafafan:20201205193121p:plain]<figcaption>テストを実行すると右側に正規表現マッチした行がキャプチャされる</figcaption></figure><
><figure class="figure-image figure-image-fotolife" title="右側でキャプチャされた行をクリックするとその地点へとジャンプして青くハイライトされる">[f:id:stefafafan:20201205193303p:plain]<figcaption>右側でキャプチャされた行をクリックするとその地点へとジャンプして青くハイライトされる</figcaption></figure><

*** ssh先に応じて背景の色とか壁紙を切り替える
サーバ上で作業していてうっかり本番環境だった……なんてことは避けたいですよね。プロファイルを切り替えてあきらかに本番環境というのがわかるようになると良いかもしれません。これはやり方がいくつかあるようです。
+ iTerm2のプロファイルを切り替えるエスケープシークエンスをssh時に実行してくれるスクリプトを自前で用意して実行する方法
+ iTerm2のShell Integrationをssh先含めあちこちの環境にあらかじめ導入した上でAutomatic Profile Switchingを設定する方法
+ 現在のusernameやhostnameを取得するiTerm2のTriggerを設定した上でAutomatic Profile Switchingを設定する方法

1番はこれだけのためにスクリプト増やすことになるし2番はあきらかに面倒そう、ということでここでは3番のやり方を紹介します。以下のサイトを参考にしました。
[https://iterm2.com/documentation-automatic-profile-switching.html:embed:cite]
[https://blog.n-z.jp/blog/2019-03-22-iterm2-automatic-profile-switching.html:embed:cite]
- まずTriggerを開いてRegular Expressionに「^(\w+)@([\w\-]+):.*\$」を設定する。これは stefafafan@some-host:/~ みたいな文字列のうちの stefafafan の部分と some-host の部分をキャプチャするための正規表現です
- Actionとしては Report User & Host、Parametersとしては \1@\2 を設定し、Instantにもチェックを入れます。
><figure class="figure-image figure-image-fotolife" title="UserとHostを通知するTriggerの設定">[f:id:stefafafan:20201205194740p:plain]<figcaption>UserとHostを通知するTriggerの設定</figcaption></figure><

- 次にProfileを増やします。Duplicate Profileから増やすのが簡単です。ここからAdvancedに移動し、下の方のAutomatic Profile Switchingを選びます。ここに例えばですが、 stefafafan@some-host という項目を追加すれば、stefafafanとしてsome-hostにsshしたらこのProfileに自動的にスイッチされるようになります
><figure class="figure-image figure-image-fotolife" title="stefafafanとしてsugoi-host-からはじまるサーバにsshしたら本番環境!!!!!というプロファイルに切り替わるような設定">[f:id:stefafafan:20201205194929p:plain]<figcaption>stefafafanとしてsugoi-host-からはじまるサーバにsshしたら本番環境!!!!!というプロファイルに切り替わるような設定</figcaption></figure><

- あとはお好みに応じてこのProfileのBackground Imageを変更するなりすれば完成
><figure class="figure-image figure-image-fotolife" title="ssh先でmysqlつないでデータを操作しようとしてるときに背景があきらかにproductionとわかるとさすがに躊躇できるはず">[f:id:stefafafan:20201205195749p:plain]<figcaption>ssh先でmysqlつないでデータを操作しようとしてるときに背景があきらかにproductionとわかるとさすがに躊躇できるはず</figcaption></figure><

** 終わり

- 自分で記事書いておきながら調べたら思いの外色々と便利そうな機能があったので満足した。使いこなしていきましょう。
- 明日は id:shallow1729:detail さんです。よろしくお願いします！
