---
Title: はてな記法のシンタックスハイライトに対応したVS Code拡張を作った
Category:
- 技術
Date: 2021-12-10T00:00:00+09:00
URL: https://blog.stenyan.jp/entry/vscode-hatena
EditURL: https://blog.hatena.ne.jp/stefafafan/stefafafan.hatenablog.com/atom/entry/13574176438041197941
---

このエントリは<a href="https://qiita.com/advent-calendar/2021/hatena" target="_blank" rel="noopener noreferrer">はてなエンジニアAdvent Calendar 2021</a>の10日目の記事です。今回はVisual Studio Code向けの拡張を作った話を書きます。

><figure class="figure-image figure-image-fotolife" title="拡張の様子">[f:id:stefafafan:20211209224634p:plain]<figcaption>拡張の様子</figcaption></figure><

====

** はてな記法そこそこ使っている
自分ははてな社員というのもあり、自社のサービスをちゃんと使っておきたいという気持ちで個人ブログもはてな記法モードで書いています。

ただ普段コード書くときはVS Codeで書いており、VS Codeは現状はてな記法で書かれた文章は認識してくれないので、中々手元で書くモチベーションがあがらない。

また、VS Code拡張作ってる人々が周辺にいたので自分も作ってみたいかもと数日前に突如思い立ちました。

** ということで作った

作りました。いますぐインストールしましょう。

[https://marketplace.visualstudio.com/items?itemName=stefafafan.hatena-syntax:embed:cite]

ソースコードはこちらです。

[https://github.com/stefafafan/vscode-hatena-syntax:embed:cite]


** 見どころ
見どころを紹介します。

まず基本的なヘッダーや箇条書きは必須ラインとして最初に実装しました。こういうの実装するときは正規表現を考えて書いていくみたいな作業のようで、例えばヘッダーはこのような正規表現を書きました。

行の先頭に <code>*</code> が1-3個あって、それ以降は <code>*</code> 以外が行末まで続く、というもの。
>|json|
"match": "^(\\*{1,3})[^\\*]*$",
||<
[https://github.com/stefafafan/vscode-hatena-syntax/blob/2114f0078d629e66e76219ab6a27a20913d2b87f/syntaxes/hatena.tmLanguage.json#L59:embed:cite]

引用なんかは複数行にまたがっているので、どうするんだろうと最初思ったのですが、開始行と終了行の正規表現を指定してあげればOKのようでした。簡単ですね。
>|json|
"begin": "^(>>)$",
"end": "^(<<)$",
||<
[https://github.com/stefafafan/vscode-hatena-syntax/blob/2114f0078d629e66e76219ab6a27a20913d2b87f/syntaxes/hatena.tmLanguage.json#L93-L94:embed:cite]

面白かったのははてなID記法で、はてなIDははてなブログタグのページに正規表現が載ってました。
>https://d.hatena.ne.jp/keyword/%E3%81%AF%E3%81%A6%E3%81%AAID>
正規表現で現すと [a-zA-Z][a-zA-Z0-9_-]{1,30}[a-zA-Z0-9] となる
<<
こちらの正規表現をお借りしつつ、最終的にはてなIDのシンタックスハイライト用の正規表現はこのようになりました。
>|json|
"match": "\\b(id:[a-zA-Z][a-zA-Z0-9_-]{1,30}[a-zA-Z0-9]:?(detail|image)?:?(large)?)\\b"
||<
[https://github.com/stefafafan/vscode-hatena-syntax/blob/2114f0078d629e66e76219ab6a27a20913d2b87f/syntaxes/hatena.tmLanguage.json#L29:embed:cite]

少し複雑になりましたが、 <code>id:</code> からはじまるべきなのと、オプションとして末尾に <code>:detail</code> か <code>:image</code> か <code>:detail:large</code> をつけることができるんですね。

また、はてなIDの正規表現をお借りしたおかげで、無効なはてなIDは色がつかないという感じになってお得でした。

><figure class="figure-image figure-image-fotolife" title="はてなID記法の様子">[f:id:stefafafan:20211209231718p:plain]<figcaption>はてなID記法の様子</figcaption></figure><

** 作ってみての感想
12/8の終業後と12/9の終業後に実現方法調べて、ちょろっとjsonファイルやREADME書いて、公開作業を行ったらすぐにVS Code拡張対応人材になれて思いのほか簡単でした。 <code>yo code</code>をお手元のターミナルで実行するだけで拡張に必要なファイル群が全部生成されて楽すぎる。

まだまだ足りない部分が多いので暇な時にもうちょっと定義を追加しておきたいと思います。READMEにちょっとだけTODO書いてます。

** 参考にしたドキュメント

公式読みましょう

[https://code.visualstudio.com/api/language-extensions/syntax-highlight-guide:embed:cite]

これだけでは具体的な文法はわからなかったりするので、TextMateのほうのドキュメントも参考になります。

[https://macromates.com/manual/en/language_grammars:embed:cite]

また、はてな記法はMarkdownのようなマークアップ言語なので、VS Codeに定義されているMarkdown用の定義もとても参考になりました。

[https://github.com/microsoft/vscode/tree/main/extensions/markdown-basics:embed:cite]

** 明日

さて明日のアドベントカレンダーの担当は id:cohalz:detail さんです。偶然にも彼もVS Code拡張を昔公開されていましたね。こちらも便利。

[https://marketplace.visualstudio.com/items?itemName=cohalz.vscode-md2hatena:embed:cite]

お楽しみに〜
