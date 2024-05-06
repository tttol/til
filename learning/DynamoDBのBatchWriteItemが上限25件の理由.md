# BatchWriteItem APIの公式ドキュメント
https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_BatchWriteItem.html#:~:text=A%20single%20call%20to%20BatchWriteItem%20can%20transmit%20up%20to%2016MB%20of%20data%20over%20the%20network%2C%20consisting%20of%20up%20to%2025%20item%20put%20or%20delete%20operations.
> A single call to BatchWriteItem can transmit up to 16MB of data over the network, consisting of up to 25 item put or delete operations.While individual items can be up to 400 KB once stored, it's important to note that an item's representation might be greater than 400KB while being sent in DynamoDB's JSON format for the API call.

BatchWriteItemは1リクエストあたり25件のデータしか送信できない。
また、1リクエストは16MB以下である必要があり、それぞれのデータは400KBまでとされている。

# 考察
### パフォーマンス
1. **パフォーマンスとスケーラビリティの確保**
DynamoDBはリクエストごとに割り当てられた最小キャパシティユニットを消費します。リクエストサイズが大きくなれば、より多くのキャパシティを消費することになります。BatchWriteItem の制限を設けることで、1リクエストあたりの最大消費キャパシティを制御し、パフォーマンスとスケーラビリティを確保しています。

2. **障害の分散とフォールトトレランス**
単一のBatchWriteItemリクエストで大量のアイテムを書き込もうとすると、リクエストが失敗した際の影響が大きくなります。25件に制限を設けることで、障害の影響を分散させ、フォールトトレランス(耐障害性)を高めています。

より多くのアイテムを一括で書き込む必要がある場合は、BatchWriteItemを複数回呼び出すことで実現できます。
ただし、プロビジョンドスループットを超えるリクエストは調整され、書き込みが遅延する可能性があることに注意が必要です。

### 結果整合性の観点
Dynamo DBはNoSQLデータベースであり、データの整合性は結果整合性である。  
結果整合性とは、DBに対する複数の書き込みが完了したのちの最終的な結果を正とする方式。つまり、RDBMSのように悲観ロックを行って強い整合性を保つわけではない。（楽観的レプリケーションとも呼ばれるらしい）  
  
https://ja.wikipedia.org/wiki/%E7%B5%90%E6%9E%9C%E6%95%B4%E5%90%88%E6%80%A7#:~:text=%E4%BE%8B%E3%81%88%E3%81%B0%E3%80%81%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%BC%E3%83%8D%E3%83%83%E3%83%88%E3%81%AEDNS%E3%80%81NTP,%E3%81%8C%E6%8E%A1%E7%94%A8%E3%81%95%E3%82%8C%E3%81%A6%E3%81%84%E3%82%8B%E3%80%82  
  
  
1リクエストで大量件数データを受け付けてしまうと整合性が保ちにくくなる？  
そもそも、複数ユーザーからの同時的な大量データ投入リクエストを受け付けるような設計をしていない？

### Dynamo DBのユースケース
RDBとの差別化ポイントはスケーラビリティの容易さと管理の楽さ、らしい。


https://qiita.com/nshinya/items/3672d127acbb6aaa8355

# 余談：プロビジョンドスループット
Dynamo DBの読み込み・書き込みのキャパシティユニットにはあらかじめ規定のスループットが設定されている。

例えば、DynamoDB テーブルのために 100 個の読み込みをプロビジョニングしているとする。
- 毎秒読み込むことができるのは、409,600 バイト (100×4 KBの読み込みキャパシティーユニットサイズ) 
- このテーブルに、20 GB (21,474,836,480 バイト) のデータが含まれており、全件SELECTすると21,474,836,480 / 409,600 = 52,429 秒 = 14.56 時間要する

このシナリオでは、DynamoDB テーブルがボトルネックになります。

https://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/EMRforDynamoDB.PerformanceTuning.Throughput.html