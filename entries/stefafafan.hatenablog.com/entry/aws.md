---
Title: AWS初心者がEC2, RDS, ALB, NAT Gatewayを使ったときに直面した様々な概念
Category:
- 技術
Date: 2016-12-11T17:31:02+09:00
URL: https://blog.stenyan.jp/entry/aws
EditURL: https://blog.hatena.ne.jp/stefafafan/stefafafan.hatenablog.com/atom/entry/10328749687198113606
---

こちらは[http://developer.hatenastaff.com/entry/engineer-advent-calendar-2016:title=はてなエンジニアアドベントカレンダー 2016]の11日目の記事です。
[http://developer.hatenastaff.com/entry/engineer-advent-calendar-2016:embed:cite]

前日は id:Windymelt:detail さんの「趣味のプログラミングから職業としてのプログラミングへ」でした。
[http://windymelt.hatenablog.com/entry/hatena-eng-advent-2016:embed:cite]

今回はAWSサービスをいくつか触ってみた話について書きたいと思います。
====

[:contents]

* やったこと
軽く自己紹介すると私は去年の夏頃はてなに入社したエンジニアで、それ以前はWeb開発の経験はなく、AWSのサービスも触ったことがなかったです。そこで簡単なWebアプリケーションを開発してみて、AWS上にデプロイしてみるということをしました。具体的な構成としてはEC2インスタンスにアプリケーションを置き、データベースはRDS、ロードバランサーはALB、そしてNAT Gatewayも使ってみました。監視はMackerelを利用しました。この際様々な概念があったのでこの記事で整理してみます。

* EC2
まずはAmazon Elastic Compute Cloud (Amazon EC2) です。クラウド上のサーバインスタンスが欲しいときに便利そうですね。
[https://aws.amazon.com/jp/ec2/:embed:cite]

使い方に関してはこちらを見ながらやると出来ると思います。
[https://aws.amazon.com/jp/ec2/getting-started/:embed:cite]

** インスタンスの作成
実際にインスタンスを起動してみましょう。

AWSにログインするとこういう画面にきます。「サービス」タブからEC2を見つけて選択しましょう。
[f:id:stefafafan:20161209151657p:plain]

EC2のダッシュボードに来ると中央にこういう良さそうなボタンがあるのでそれをクリックするとインスタンスを作るウィザードに遷移します。
[f:id:stefafafan:20161209151733p:plain]

こういう画面がでてくるのでわかりやすいですね。
[f:id:stefafafan:20161209151748p:plain]

よくわからない概念がいろいろ増えてきました（VPC、サブネット、IAMロール、セキュリティグループ）デフォルトのままでもなんとかなったりしますが、それぞれ見ていきましょう。
[f:id:stefafafan:20161209151809p:plain]
[f:id:stefafafan:20161209151823p:plain]

* VPC
Amazon Virtual Private Cloud (Amazon VPC)です。以下の記事に簡単な説明があります。
[https://aws.amazon.com/jp/vpc/:embed:cite]

VPC内の構成はユーザ次第という感じで、その中にサブネットを配置して、EC2インスタンスなどを起動するということができます。

** VPCの作成
元々デフォルトのVPCがありますが、追加で作りたい場合はEC2インスタンスを作るときと同様に、今度は「サービス」から「VPC」を選択し、VPCダッシュボードに行きます。
[f:id:stefafafan:20161209161204p:plain]

「VPCウィザードの開始」から作る、もしくはサイドバーの「VPC」を選択して「VPCの作成」を選ぶ方法があります。ウィザードを利用したほうがわかりやすそう。パブリック、プライベートサブネット？
[f:id:stefafafan:20161209161914p:plain]

* サブネット
サブネットとは何かという感じですが、以下の記事を読むとVPCとの関係がわかりやすいです。
[http://docs.aws.amazon.com/ja_jp/AmazonVPC/latest/UserGuide/VPC_Subnets.html:embed:cite]

** サブネットの作成
VPCダッシュボードからサブネットを個別に作ることができます（先ほどのVPCウィザードで一緒に作ってしまっても良さそう）
[f:id:stefafafan:20161209165105p:plain]

パブリックとプライベートの違いですが、上記サイトによると、
>>
サブネットのトラフィックがインターネットゲートウェイにルーティングされる場合、そのサブネットはパブリックサブネットと呼ばれます。
<<
のようです。インターネットゲートウェイ。ルートテーブル。

* インターネットゲートウェイ
[http://docs.aws.amazon.com/ja_jp/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html:embed:cite]
>>
VPC のインスタンスとインターネットとの間の通信を可能にする VPC コンポーネントであり
<<
なるほどですね。インターネットと通信する場合は必要そうですね。

** インターネットゲートウェイの作成
[f:id:stefafafan:20161209164559p:plain]
先ほどのVPCダッシュボードからインターネットゲートウェイを選び、作成はネームタグを決めるだけなので簡単にできます。またここでVPCにアタッチ・デタッチもできます。

* ルートテーブル
ネットワークトラフィックを指定するもののようですね。
[http://docs.aws.amazon.com/ja_jp/AmazonVPC/latest/UserGuide/VPC_Route_Tables.html:embed:cite]

つまりこのルートテーブルにてサブネットのトラフィックをインターネットゲートウェイに向ければパブリック・サブネットにできるという感じですね。

** ルートテーブルの作成
ルートテーブルもVPCダッシュボード「ルートテーブル」の欄から作成できます。以下のような「ルート」タブがあるので、ルーティングを指定できます。
[f:id:stefafafan:20161211101417p:plain]

* IAMロール
IAMロールに関しては以下に詳しくありますが、どのユーザがどのリソースにたいしてアクセス権限があるのかを制御するのに使うようですね。後ほどサーバ監視・管理サービスMackerelの話でもこちらのIAMロールが出てきます。
[http://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/id_roles.html:embed:cite]

IAMロールの作成や管理はこちらからできます。
[https://console.aws.amazon.com/iam/]

* セキュリティグループ
セキュリティグループですが、トラフィックを制御するという点ではルートテーブルやサブネットとごっちゃになりそうですが、サブネットレベルではなくインスタンスレベルで制御するようです。
[http://docs.aws.amazon.com/ja_jp/AmazonVPC/latest/UserGuide/VPC_SecurityGroups.html:embed:cite]

サブネット内にインスタンスが複数個ある際にインスタンスごとに細かくトラフィックを制御したいときに便利そうですね。

** セキュリティグループの作成
VPCダッシュボード…かと思いきやEC2ダッシュボードにあります。「インスタンスごと」の制御なので確かに、という感じですね。作成時こういう画面が出るので、インバウンド、アウトバウンドのトラフィックの制御ができます。

[f:id:stefafafan:20161211103346p:plain]

* RDS
次にAmazon Relational Database Service (RDS)です。アプリケーションがデータベースを利用している際は便利そうですね。6つのデータベースエンジンから選択することができるようです。
[https://aws.amazon.com/jp/rds/:embed:cite]

** RDSの作成
EC2の作成の際と同様に、RDSダッシュボードに行き、インスタンスの起動を選択します。「今すぐ始める」を選択しても同じみたいです。
[f:id:stefafafan:20161211105334p:plain]
[f:id:stefafafan:20161211105347p:plain]

私の場合はアプリケーションでMySQLを利用していたのでMySQLを選択しました。
[f:id:stefafafan:20161211105445p:plain]

MySQLのエンジンのバージョンやストレージ、識別子など聞かれるので入力していきます。
[f:id:stefafafan:20161211105902p:plain]
[f:id:stefafafan:20161211105911p:plain]

ネットワーク＆セキュリティ欄にてVPC、サブネット、セキュリティグループなど聞かれます。これらはEC2の際にもみた概念ですね。自分の構成に合わせて選択していくと良さそうです。
[f:id:stefafafan:20161211110341p:plain]

あとは作成ボタンを押せば完了。少し待てばDBインスタンスを利用できるようになります。本当にクリックだけで利用できるようになって便利です。

* ALB
次にApplication Load Balancerです。名前の通りロードバランサーなので、サービスに対するリクエストを分散させたりできます。
[https://aws.amazon.com/jp/elasticloadbalancing/applicationloadbalancer/:embed:cite]

** ALBの作成
EC2ダッシュボードに「ロードバランサー」という項目があるのでそこから作成できます。以下のような画面になるのでApplication Load Balancerを選択。
[f:id:stefafafan:20161211113458p:plain]

アベイラビリティゾーンという概念が登場します。ALBで二つ以上のゾーンを指定することによって、このリクエストは1a、これは1cなどのように振り分けることができるようです。
[f:id:stefafafan:20161211113515p:plain]

ALBのセキュリティグループも指定できます。EC2やRDSの場合もそうですが、それぞれセキュリティグループを適当にデフォルトのものなどにしているとわからなくなってくるので、これはアプリケーションのあるEC2インスタンスのセキュリティグループ、これはDBインスタンスのセキュリティグループなどとわかりやすく名前などもつけておくと良いと感じました。
[f:id:stefafafan:20161211113832p:plain]

ターゲットグループとヘルスチェックという概念が登場します。
[f:id:stefafafan:20161211114110p:plain]

** ターゲットグループ
ALBでリクエストをルーティングする対象のEC2インスタンスなどが「ターゲット」なので、それらをまとめたものが「ターゲットグループ」という感じですね。
[http://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/load-balancer-target-groups.html:embed:cite]

また1つのALBにつきターゲットグループは複数指定することも可能のようです。

*** ターゲットグループの作成
先ほどALBを作成する際に一緒に作成することも可能ですし、ALBを作成したあとにEC2ダッシュボードの「ターゲットグループ」から新たに作成することができます（大体同じ画面にきます）。VPCなども適切に指定してあげましょう。ターゲットグループを作成した後、個別のターゲットをグループに入れていけば良いです。
[f:id:stefafafan:20161211120638p:plain]

** ヘルスチェック
定期的にターゲットグループ内のターゲットに対してリクエストを送信し、正常かどうかを確認することがヘルスチェックとのことです。
[http://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/target-group-health-checks.html:embed:cite]

自分のアプリケーションが一部のターゲットグループではうまく動作していない場合、そちらにリクエストがいくようになってると困りますよね、そういうことが起きないようにヘルスチェックをし、そちらにはリクエストをいかせないようにALBがやってくれるようです。便利ですね。

** ALB vs. ELB, CLB
ALBについて調べていると、ELBやCLBのような用語も登場すると思います。どうやら元々ELBというものがあり(Elastic Load Balancer), 最近になってALBが追加された(Application Load Balancer)ので、それに従って今までELBと呼ばれていたものがCLBとなり(Classical Load Balancer)、現在ではCLBとALBがどちらもELBの一種という位置付けになっているみたいです。

ALBとCLBの具体的な違いについては以下にまとまってあります。
[http://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/userguide/what-is-load-balancing.html:embed:cite]

* NAT Gateway
最後にNAT Gatewayです。以下の記事にも書いてありますが、これを利用すればインターネットからはアクセスできないようにしつつ、インスタンスからインターネットや他インスタンスにアクセスできるよう制御できて嬉しいです。
[http://docs.aws.amazon.com/ja_jp/AmazonVPC/latest/UserGuide/vpc-nat-gateway.html:embed:cite]

** NAT Gatewayの作成
こちらはVPCダッシュボードから作ります。パブリックサブネットとElastic IPを指定するだけで作れます。
[f:id:stefafafan:20161211124913p:plain]

ここまでの概念がわかれば以下の画像もわかりやすいかと思います。プライベートサブネットのインスタンスがNAT Gatewayを利用しトラフィックを送信していることがわかります。ルートテーブルが正しく指定されていることも大事です。
[http://docs.aws.amazon.com/ja_jp/AmazonVPC/latest/UserGuide/images/nat-gateway-diagram.png:image=http://docs.aws.amazon.com/ja_jp/AmazonVPC/latest/UserGuide/images/nat-gateway-diagram.png]

** NAT Gateway vs. NAT Instance
以下の表がわかりやすいです。元々NAT Instanceがあり、NAT Gatewayの登場によりメンテナンスコストが減ったようです。
[http://docs.aws.amazon.com/ja_jp/AmazonVPC/latest/UserGuide/vpc-nat-comparison.html:embed:cite]

* 監視
ここからはおまけみたいな感じですがとても大事だと思います。

AWS上にアプリケーションをデプロイし、色々なサービスを利用したは良いけど、障害などが起きた際にどこが問題なのか知るにはどうすれば良いでしょうか。やはり監視はしておきたいですね。AWS Cloudwatchでグラフをみたりアラームを設定したりもできますが、今回はMackerelを利用してみました。

** Mackerel
Mackerelははてなが運営するサーバ監視・管理サービスです。
[https://mackerel.io/:embed:cite]

ちょうど昨日のMackerelアドベントカレンダーでもMackerelのサービスメトリック、監視設定、アラートについて触れましたので、そちらも参考にしてください。
[http://stefafafan.hatenablog.com/entry/2016/12/10/140000:embed:cite]

*** mackerel-agent
Mackerelの基本的な構成としてはエージェントをサーバにインストールし、メトリックを定期的にMackerel本体へプッシュしていくという感じです。エージェントのインストール方法に関してはMackerelにログインし、[https://mackerel.io/my/instruction-agent:title=「新規ホストの登録」画面]に行くとOSごとにインストール方法が記載されています。
[f:id:stefafafan:20161211131104p:plain]

あとは設定ファイルにAPIキーを追記し、エージェントを起動させると勝手にグラフに様々なメトリックが描画されます。

エージェントに関してはオープンソースです。
[https://github.com/mackerelio/mackerel-agent:embed:cite]

プラグインなども充実しています（自分のプラグインを作成して利用するのも可能）
[https://github.com/mackerelio/mackerel-agent-plugins:embed:cite]

*** AWSインテグレーション
EC2インスタンスなどにエージェントをインストールしてメトリックを取得するのも便利ですが、今回のようにAWSのサービスを複数利用している場合はMackerelのAWSインテグレーションを利用するとより便利かと思います。
[https://mackerel.io/ja/docs/entry/integrations/aws:embed:cite]

連携の仕方としては推奨されている方法はIAMロールを指定するというものです。IAMロールを利用すると、権限を制限することができますので安心です（EC2の場合はAmazonEC2ReadOnlyAccessなど）。

オーガニゼーション設定の[https://mackerel.io/my?tab=awsIntegration:title=AWSインテグレーションタブ]から設定できます。
[f:id:stefafafan:20161211132154p:plain]

IAMロールやリージョンを選択し、メトリックを取得したいAWSサービスにチェックを入れると以下のように取得されるインスタンスの数が表示されるので実際に設定を作る前に確認できます。
[https://f.st-hatena.com/images/fotolife/m/mackerelio/20160617/20160617104510.png:image=https://f.st-hatena.com/images/fotolife/m/mackerelio/20160617/20160617104510.png]

あとは数分待てば勝手にグラフができます。便利ですね。

* まとめ
AWSサービスは沢山あり、知らない用語や概念も多いですが、一度利用してみると他のサービスもすぐに使えるようになると思うので簡単なものから試すと良さそうと思いました。また、先日のイベント「AWS re:Invent 2016」でも新しいサービスが増えたのでそれらも触ってみたいですね。

最後にMackerelの話を少ししたところで明日はちょうどMackerelのディレクター兼チーフエンジニアのid:Songmu:detailです。どんな話をするのでしょうか、楽しみですね。
