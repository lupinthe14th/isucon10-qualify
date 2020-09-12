# isucon10-qualify

## やったこと
時系列ではなく思い出すママに列挙。

- git関連準備
- fishインストール
- nginxでL7で振り分け
  - server2、3は直接1323を呼ばず、nginxを通した
- server３をmysqlサーバにして、ウエイトを下げたapiサーバを動作させた
  - server[1|2]から接続できなくて少しハマる
    - bind-addressを外向きのインタフェースのipに変更
    - create user isucon ..., grant ... して対処
  - リクエストタイムアウトになった/api/estate/nazotteをserver3のみに割り振った
- nginxのパフォーマンスチューニング設定
  - alpのログ設定入れたけど読みきれなかったのでアクセスログはoffにした
- mysqlのスロークエリ出力
  - 遅いのがなくて、一番遅いselectでも実行計画見つつwhere句にindex追加しても効果なし
  - order by のインデックス追加しても効果なし
  - てか、逆に遅くなった
- ORDER BY a desc,b asc LIMIT ? OFFSET ? をgoで実装
  - ここでmysqlのcpuを使っていると判断して、アプリ側でやって負荷ポイントを変えてみることにした
  - 実装がいけてなくて何度もレスポンス不正のバグを作っていた
  - 何とか実装できたけどmysqlの負荷は下げられたけど、スコアは下がった。 :cry


## ネタバレ知見
- apparmorサービスが動いていたとのこと
  - 何これ初耳でしたが、SELinuxみたいなのらしい
  - 無効化するべきものらしい
  - https://discordapp.com/channels/729982721924792320/729982721929117794/754315180799819826
- mysqlを2つに
  - 2テーブルしかないから思い切って二つに分けるとかやると良いらしい
- https://discordapp.com/channels/729982721924792320/729982721929117794/754319077719277596
