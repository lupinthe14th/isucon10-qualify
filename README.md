# isucon10-qualify

## やったこと
時系列ではなく思い出すママに列挙。

- git関連準備
- fishインストール
  - 主に操作したserver1だけ
- nginxでL7で振り分け
  - server[2|3]は直接1323を呼ばず、nginxを通した
- server3をmysqlサーバにして、ウエイトを下げたapiサーバを動作させた
  - server[1|2]から接続できなくて少しハマる
    - bind-addressを外向きのインタフェースのipに変更
    - create user isucon ..., grant ... して対処
  - リクエストタイムアウトになった/api/estate/nazotteをserver3のみに割り振った
    - mysqlとの接続をネットワーク経由しない方が遅延なくせると思ったため
    - 効果は多少あったのかリクエストタイムアウトは無くなった
- nginxのパフォーマンスチューニング設定
  - alpのログ設定入れたけど読みきれなかったのでアクセスログはoffにした
- mysqlのスロークエリ出力
  - 遅いのがなくて、一番遅いselectでも実行計画見つつwhere句狙いのindex追加しても効果なし
  - order by句狙いのインデックス追加しても効果なし
  - てか、逆に遅くなった
- ORDER BY popularity desc,id asc LIMIT ? OFFSET ? をgoで実装
  - ここでmysqlのcpuを使っていると判断して、アプリ側でやって負荷ポイントを変えてみることにした
  - 実装がいけてなくて何度もレスポンス不正のバグを作っていた
  - 何とか実装できてmysqlの負荷は下げられたけど、スコアは下がった。😭


## ネタバレ知見
- apparmorサービスが動いていたとのこと
  - 何これ初耳でしたが、SELinuxみたいなのらしい
  - 無効化するべきものらしい
  - https://discordapp.com/channels/729982721924792320/729982721929117794/754315180799819826
- mysqlを2つに
  - 2テーブルしかないから思い切って二つに分けるとかやると良いらしい
- ORDER BY popularity desc,id asc LIMIT ? OFFSET ?
 - https://discordapp.com/channels/729982721924792320/729982721929117794/754319615051432046
 - https://discordapp.com/channels/729982721924792320/729982721929117794/754319077719277596
