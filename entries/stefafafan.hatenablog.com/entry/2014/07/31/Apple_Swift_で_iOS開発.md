---
Title: Apple Swift で iOS開発
Category:
- 技術
Date: 2014-07-31T08:13:09+09:00
URL: https://blog.stenyan.jp/entry/2014/07/31/Apple_Swift_%E3%81%A7_iOS%E9%96%8B%E7%99%BA
EditURL: https://blog.hatena.ne.jp/stefafafan/stefafafan.hatenablog.com/atom/entry/12921228815729202047
---

久しぶりのブログ更新。7/7から立命館大学にて"短期留学生"として研究をしていました。  
自分の大学ではB4になったらシニアプロジェクトというのがあって、いわゆる卒論の代わりみたいな感じになります。僕はそのプロジェクトのために立命館大学にいま来ています。ここ3週間ちょいで色々進捗あったので忘れないうちにブログにメモっておきます。  


<!-- more -->


#### 目次

1. 構想
2. 開発環境
3. サーバー構築
4. iBeacon
5. HTTPリクエスト
6. JSON
7. アカウント管理
8. 地図
9. 戦闘システム
10. M7チップ
11. ネットワーク通信
12. これからの事

##### 1. 構想
自分の大学ウースター工科大学と立命館大学の教授達と一緒に話し合った結果、プレイヤーが遊びながら健康的になれるようなゲームが作れたらいいなという結論に至りました。具体的には現実世界でリアルな友人と一緒に歩きまわって、iPhoneアプリ内で敵とエンカウントして倒したりお宝を集めたりするゲームです。簡単に言うと万歩計にRPG要素を入れた感じになります。

##### 2. 開発環境
このアプリを作る上で僕達はAppleのiBeaconを使ってみようと思いました。ですがそれを利用するにはiOSデバイスが必要になるので必然的にiOSアプリを開発することになりました。自分は6月頃に二日間ほどObjective-Cを触ってハッカソンでアプリを作成したことがありますが、一緒に立命館大学にきた友人は全く触ったことがなかったので、ここは思い切ってAppleから発表されて間もないSwiftを使っちゃおうってことで決まりました。あまり深く考えずに決めましたがいざとなってはObjective-Cのコードも共存できるので大丈夫なはずです。

##### 3. サーバー構築
アプリを作るにあたってサーバーが必要になってくるだろうなって最初に思ったので研究室のパソコンをサーバーにさせてもらいました。正直僕達はサーバーについての知識はほとんどなく（友人はPHPをある程度使えて僕はJavaScriptはできるけどサーバーサイドの経験は少なかった。2人ともデータベースの授業でMySQLはかじった程度）、ググりながらの作業になりました。最終的にUbuntu Serverをインストールしました。

[https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu:title]  
[https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-phpmyadmin-on-ubuntu-12-04:title]

##### 4. iBeacon
僕達のアプリは近くに居る友人と遊べる機能がつく予定なのでiBeaconを利用することにしました。iBeaconとSwiftを使ったサンプルアプリが幸いネットにありました。

[http://qiita.com/oggata/items/5de43d71692d1abcff7c:title]

##### 5. HTTP リクエスト
いくらサーバーがあってもiPhoneアプリのほうからデータを送ることができなければ意味がないのでSwiftでHTTP POSTなどが実装できるか頑張った結果案外すぐできました。このへんで薄々気づきだしましたが、Swiftに関することをググるとわりとQiitaというサイトがヒットするのでStackOverflowの次にマークしておこうと思いました。

[http://qiita.com/mochizukikotaro/items/e2da2d3186ec24e291a6:title]  
[http://www.brianjcoleman.com/tutorial-post-to-web-server-api-in-swift-using-nsurlconnection/:title]

##### 6. JSON
JavaScriptが好きな自分はデータベースの情報はJSONファイルに出力したら便利なんじゃないか？と思ったのでPHPerの友人にそうしてもらいました。これでiPhoneアプリはそのリンクをアクセスしてJSONファイルをゲットしてちょちょいとJSONをパースできる…んだと思ってましたが予想外にSwiftでのJSONの扱いが大変だったのでここでちょっと苦労しました。JSONの複雑さにもよりますがイメージ的には以下のようにしないといけない感じでした：

```objectivec
var jsObj = NSJSONSerialization.JSONObjectWithData(jsonData, options: NSJSONReadingOptions.MutableContainers, error: &error)
if let hoge = jsObj as NSDictionary {
    if let piyo = hoge["fuga"] as NSArray {
        /* 処理 */
    }
}
```
未だにif let...っていうのに違和感を感じています。

#### 7. アカウント管理
沢山の人のデータを管理するためには1人1人をなんらかの方法で区別する必要があります。このためには例えばユーザーIDを作ってもらってそれでログインさせるというのもアリですが、そのためには他人が自分のアカウントにログインしないようにパスワードも必要になってきます。ですがそういう情報はなるべく自分たちで管理したくないなと思いました（少なくともパスワードは暗号化する必要がありますしデータベースが流出したときのリスクを考えなければならない）。次に考えたのはiPhone自体の固有のIDのようなものを取得することです。検索しているとデバイストーケンというものがあるらしいですが、これを取得するのに意外と長い手順を踏まないといけないことが判明しました。最終的に僕達はアップルのAccountsフレームワークというものを使ってiPhone自体に保存されているフェイスブックやツイッターのアカウント情報を利用しようと決めました。実際にアプリからツイッターやフェイスブックへ投稿がしたかったらSocialフレームワークも多分必要になってきますが今回はログイン情報だけということでAccountsフレームワークだけで大丈夫そうです。

[https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html:title]
[http://qiita.com/exilias/items/b574e983c18b9360fe34:title]  
[http://qiita.com/135yshr/items/54a999b334f50f1709d5:title]

#### 8. 地図
プレイヤーの現在地や周りのプレイヤー・iBeaconの場所をみせるために地図も表示しなければならないです。僕たちはまず
[Google Maps SDK for iOS](https://developers.google.com/maps/documentation/ios/) というものを導入しようと試みました。結論からいうと失敗しました。まずこのSDKは完全にObjective-Cで書かれているのですが、SwiftからObjective-Cを呼び出すのにBridging-Headerというファイルを作成してやらないとできないらしいのです。その通りにとりあえず書いてみて中々地図がアプリに表示されなかったです。ではとりあえずこのSDKについているサンプルコードをObjective-Cだけど実行してみようと思ってもなぜかそれすら上手く実行できませんでした。ここでこんなにつまずくのは時間が無駄になるなと思い、普通のiOSのMapKitを使おうかということになりました。そしたら結構パパっと書けたのでよかったです（あとでもし時間に余裕があればMapKit以外のものも試してみるかもしれません）。これで現在地と他のピンを表示させるアプリが作れます。あとは地図からプレイヤーの座標を検出してデータベースへPOSTなどもやってみました。

[http://qiita.com/oggata/items/18ce281d5818269c7281:title]

#### 9. 戦闘システム
戦闘システムについては基本的に僕の友人がずっと作ってたので詳しいことは言えませんがひとつ言えることは最初に彼はものすごくコンプレックスなゲーマー向けのRPGのようなものを考えていたのですが教授らからの指摘でシンプルにまとめました。まずこのアプリを作成するのに3ヶ月ほどしかないというのもありますし、歩きながら遊ぶアプリで歩きがメインなのでそこまでゲームをヘビーゲーマー向けにする必要はないということです。まあなんにしろ9月下旬までにはちゃんと動作するアプリが完成していることを祈っています。画面へのスプライトの表示には最近SpriteKitというのを使い始めました。

#### 10. M7チップ
iPhone 5SからはM7チップというものが導入されたらしく、このチップはiPhoneがスリープ状態などのときでも歩数や現在の移動状況についての情報をずっと蓄積しているらしいです。なのでこれを利用すれば自分で歩数を数えるプログラムを書く必要がなくなりますし（そうする場合はちゃんと歩くときのノイズに対応するためにローパスフィルターなどをかけるようです）、今までは歩数を数えるためにはずっとiPhoneのバックグラウンドでアプリを起動しておく必要があったため電池をたくさん食いましたがこれを使えばその心配はないです。もちろん、これを使ってしまうとプレイヤーのターゲットをiPhone 5S利用者にしぼることになってしまいますが、この短期間の研究期間なのでとりあえずはやむを得ないと思います。時間が余ればほかのiPhoneのための対策も考えるかもしれません。  
一つちょっと引っかかったのはよくwithHandlerというパラメータがここで登場したのですがSwiftで書き換える場合クロージャにしないといけない模様だったのでちょっとシンタックスなどを気をつける必要がありました：

Objective-C
```objectivec
[self.cmStepCounter startStepCountingUpdatesToQueue:self.operationQueue updateOn:1 withHandler:^(NSInteger numberOfSteps, NSDate *timestamp, NSError *error) {
    [[NSOperationQueue mainQueue] addOperationWithBlock:^{
        /* 処理 */
    }];
}];
```

Swift
```objectivec
cmStepCounter.startStepCountingUpdatesToQueue(operationQueue, updateOn: 1, withHandler: {numberOfSteps, timestamp, error in
    /* 処理 */
})
```
結果的にSwiftのコードがかなりシンプルになってるのが嬉しいです。self.cmStepCounterのように明示的に書く必要もないし、numberOfStepsがNSIntegerだとかも書かなくても大丈夫です。withHandlerのとこで{}を使ってクロージャにしているというのだけ気をつければ大丈夫でした。


[http://stackoverflow.com/a/20938104:title]  
[http://griffin-stewie.hatenablog.com/entry/2013/09/22/130002:title]  
[http://wonderpla.net/blog/engineer/iPhone5s_M7chip/:title]

#### 11. ネットワーク通信
サーバーはあるのでそれ利用して色々できますが、複数の人でネットワークプレイのようなことするのは難しいかなと思い色々ググっていました。そしたら色んなサイトにてGameKitを使えば簡単にネットワーク対戦ができると知ったので使ってみることにしました。とりあえず簡単なプログラムをつくってみようと思ったその瞬間です。GKSessionがどうやらdeprecatedだったようです。結局[StackOverflow](http://stackoverflow.com/a/18939626/3030241)の回答をみてMultipeer Connectivityフレームワークを使ってみようと決めました。見た感じiOS6まではGKSessionが使えて、iOS7からはMultipeer Connectivityしか使えないようなので互換性では問題がありますが、M7チップのCore Motionフレームワークを使ってる時点でiPhone 5Sに限定しちゃっているのでとりあえず問題ではなさそうです。

[http://giginet.hateblo.jp/entry/2013/01/23/053050:title]  
[http://stackoverflow.com/questions/18939472/gksession-is-deprecated-in-ios7-what-should-i-use-now:title]

ググっているとMultipeer Connectivityフレームワークを使ったサンプルプログラムのソースを記載している人がいたので彼のコードをお借りしてSwiftで書き直して実行してみたらちゃんと動作確認できました。これはつい二日前のことです。

[https://hirooka.pro/?p=3264:title]

#### 12. これからの事
この数週間でサーバー系のことをやったりSwiftでシンプルなプログラムを沢山作ったりして充実していましたがそろそろバグだらけでも良いのでそれなりに遊べるような超アルファ版みたいなのを完成させたい気分です。そのためにもSpriteKitももっと触って色々とみんなと力を合わせてがんばりたいですね。ずっとコーディング漬けみたいな印象を受けるかもしれませんがしっかりとゲーセン行ったり日本橋や清水寺訪ねたりしてるので結構楽しんでます。
