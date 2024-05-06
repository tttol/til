- https://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/cognito-user-pools-using-import-tool.html
- 上記リンクを参考に実施
1. Cognitoの画面からユーザーインポートを選択
2. ジョブ名とサービスロール名を指定
3. CSVをアップロードする
   - CSVは*template.csv*からDLしたものをベースに作る
   - 必須項目だけ埋めてあとは空でOK
4. ジョブを実行する。成功・失敗はCloudWatchLogsに記録される。 