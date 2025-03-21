---
title:  'Astro & Cloudflare R2 に引っ越し'
description: '後に大物ブロガーになる男 v2'
pubDate: '2025-02-08'
heroImage: '/blog-placeholder-2.jpg'
---

### Next.jsが覇権だと言ったな。
### あれは嘘だ。

最初は覇権だなぁと思って僕から採用したんです。
恐ろしいフレームワークだった。

#### AstroとCloudflare Pages に移行しました

Next.js使ってたんですけど、オレルールが多く、よくわからなくなってきたので、スタティックサイトジェネレーターに戻りました。また引っ越し。趣味はブログの引っ越し。全然関係ないですけど、趣味は引っ越しの友人がいます。昔ヒッコスで引っ越すっていう絵本ありましたよね。(´･_･`)

ちな、昔使っていたhexoよりもナウいのが、このAstroと呼ばれるフレームワークらしい(hugoはおじさんしか使ってないらしい)。素のReact.jsの知識がつきそうな感じがします。少しずつ改変していますが、結構気に入ってます。

スタティックサイトジェネレータのまとめサイトの[JamStack](https://jamstack.org/generators/)にて、各フレームワークの勢いが見れるらしいので、こちらに再掲します。これから大物ブロガーを目指す人はぜひチェックしてください(´･_･`)

#### 当初のモチベーション

ブログを再び書こうと決意したものの、GithubPagesでは画像の上限なんかがかなり厳しいので、外部ストレージを使いつつ、いい感じにホスティングできないかと考えていました。

自分のユースケースでは、静的なサイトをCloudflare Pagesで表示し、画像や動画は外部クラウドに配置するという方法が安牌そう。以前にもCloudflare Pagesを使っていて、デプロイが簡単だったので、今回はCloudflareの他サービスも使ってみようと意気込む。そのため、画像はR2に配置し、必要に応じてその都度読む作戦。

しかし、ここからが長かった。そもそもCloudflareの機能が多すぎて、何の機能がどこまでカバーしているかがあまりわからない。また、AWSなどのクラウド基盤の使い方が全くわかっていなかったので、いまいちピンと来ないことが多い。めっちゃ大変だった。(´･_･`)

調べていくうちに、AWSと比較して、Cloudflareは静的ストレージが安い。しかも、ドメインの登録、R2ストレージの連携も容易でした。全部Cloudflareでまとめて良かったです。以前はドメインにムームードメインを使っていましたが、Webサーバーとの連携も手動で結構面倒だったので、それを思うと感動しました。Cloudflare包囲網恐るべし(´･_･`)。

全然関係ないんですけど、ムームードメインでドメインを買っていたとき、購入時の安さに飛びついて.techのドメインを購入したのですが、年一の更新料金がその4倍して目ん玉飛び出た記憶があります（筆者の確認不足だけだった説が濃厚）。Cloudflareではどちらも購入時に見やすく記載してくれていたので、安心して更新できる.comドメインを購入しました。

今回は三日坊主にはならないよう更新していくぞう(´･_･`)