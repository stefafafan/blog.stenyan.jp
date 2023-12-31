---
Title: Atomエディタを使ってみる - その2
Category:
- 技術
Date: 2015-07-28T21:26:24+09:00
URL: https://blog.stenyan.jp/entry/2015/07/28/Atom%E3%82%A8%E3%83%87%E3%82%A3%E3%82%BF%E3%82%92%E4%BD%BF%E3%81%A3%E3%81%A6%E3%81%BF%E3%82%8B_-_%E3%81%9D%E3%81%AE2
EditURL: https://blog.hatena.ne.jp/stefafafan/stefafafan.hatenablog.com/atom/entry/8454420450103726803
---

昨夜と同様に、[https://atom.io/docs/v1.0.2/:title=Atom Flight Manual]の続きを読んでAtomの使い方をもっと深く理解していこうとおもいます。
今日は2.6章から2.14章まで読みました。

====

* Snippet 作業を一気に効率化
Atomでhtmlファイルを開いた後、<b>html</b>と入力してみてタブを一回押すとこうなります：
>|html|
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title></title>
  </head>
  <body>
    
  </body>
</html>
||<

えっ、便利…ってなりました。色んな言語に応じてテンプレートみたいな感じでどんどん出力してくれるので便利ですね。しかも自分のSnippetも簡単に定義できるのでいっぱい活用していきたい！

* バージョン管理
AtomはどうやらデフォルトでGitといい感じに連携しているので、ステータスバーに前回のコミットから編集した行数が表示されていたり、追加・編集・削除した行などは左側が緑・黄・赤の色がついたり（前からこれはなんなんだと思っていました）、あとはGitHubを利用している場合ショートカットで簡単に現在開いているファイルのGitHubページを開いたりと色々できて便利っぽいです。ただまあこれに関してはターミナルからgitでごにょごにょすることに慣れている人ならあまり必要じゃないかも？と思いました。

* 文章を書く
AtomはMarkdownにも対応していて、英語の簡単なスペルチェックやMarkdownのシンタックスハイライトのほかに、実際にMarkdownで書いているページのプレビューをリアルタイムで横に表示させながら書いたりもできるようです。Markdownエディタについて全然詳しくないですが、これはとても便利そうですね。

* 簡単なカスタマイズ
AtomはほぼChromiumブラウザみたいなものなので、ちょっとした色とかのスタイルを変更したかったらlessファイルを編集すればいいようです（CSSも可）。デベロッパーツールとかも見ながらできるので普段からブラウザ触ってるWeb系の人とかにはもってこいですね。


ってところで第2章も読み終えました。Emacsとかvimと比べて結構楽に使えてでも高機能でとてもよさそうですね！次回からは第三章のHacking Atomに入るのでいよいよ上級者向けかな？って感じです。
