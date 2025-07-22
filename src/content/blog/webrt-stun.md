---
title: 'WebRTCのNAT越えに失敗した'
description: '助けてSTUN'
pubDate: '2025-02-13'
heroImage: 'https://docteu.com/blog-placeholder-4.jpg'
---

#### 閉域でWebRTCを使う
以前の記事で少し話題にしたのですが、閉域でWebRTCを開発しています。しかし、ネットワーク構成的に、シグナリングあれば行けるっしょとタカを括っていたのですが。。。

##### シグナリングはうまくいっているのに動画もデータ通信も始まらない！
…どういうことだってばよ？
シグナリングサーバのログで、Webソケット通信が試みられた場合にログを出力しており、ここは問題なし。OfferがAnswerのSDPやICEを受け取ったのも確認。

##### なにか大切なことを忘れている気がする(´･_･`)
このOfferとAnswerとしているPeerたちは、AnswerからOfferへはプライベートIPで通信できるが、逆はOfferからAnswerの間にいるルーターがNAT機能を持っているため、送信と受信の経路が異なります。一方は直接通信できるが、逆方向はNATの影響で通信できないネットワークになっています（これを*非対称ルーティング*という）。

片側が通信できるからとタカを括っていたツケを払わされる。

ここで、
```JavaScript
peerConnection.onicecandidate = (event) => {
    if (event.candidate) {
      console.log("Generated ICE Candidate (Offer):", event.candidate);
    } else {
      console.log("All ICE candidates sent (Offer).");
    }
  };
  ```
  このようにICE Candidateを監視しておくと、ICEの詳細なデータをevent.candidate上で見ることが可能です。
  それを確認すると
  ```ICE Candidate
  {
    ...
  typ: host
  ...
  }
  ```
  という記述があります。typがhostとなっている際は、ローカルネットワーク内で直接通信できることを表します。しかし、NATの影響がある非対称ルーティングの場合は考慮することができません。

  ※お恥ずかしながら、NAT機能は、インターネットとプライベートネットワークの変換のことを指すと思っていましたが、今回のようにインターネットを介さない場合の異なるセグメントを中継する際にもNATが使われるらしいです(´･_･`)。

  ##### 閉域ネットワークだから！（NATを越えないとは言っていない）

  まさしくこのようなネットワークだったというワケです。このような場合、STUNサーバを用意して、NATの変換前/変換後の情報を組み合わせて、NATを越えてあげる必要があるわけです。
  
  ですが、閉域ネットワークなので、天下のGoogleがホスティングしているサーバは使用できません。そのため、COTURNなどを使用して、閉域にSTUNサーバを設置する必要があるかなと考えています。 また、STUNはシグナリングのようにWebソケットサーバを自前で用意する必要がなさそうです。以下のように、Peerインスタンスを作成する際に、iceServersを追加し、サーバー名とポートを指定するだけで良さそうなので、書くだけなら楽チンそうです(´･_･`)。
 ```JavaScript
  const peerConnection = new RTCPeerConnection({
  iceServers: [{ urls: "stun:stun.l.google.com:19302" }] 
}); //↑を自前で用意したCOTURNサーバのIPにする
```
  
  ちなみにSTUNサーバを使用すると、ICE Candidateのtypが変わり、
  ```ICE Candidate
    {
    ...
  typ: srflx
  ...
  }
  ```
  
  というタイプになるらしいです。さらに 外側から見えているIPに加えて、raddrという項目に変換前のプライベートIPも含まれます。これを確認すれば、STUN が正しく動作しているか分かるはずです。STUNによるNAT越えがうまくいっているかは、引き続きICE Candidateを確認すれば良さそうですね。戦いは続きます(´･_･`)。