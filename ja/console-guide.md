## AI Service > AI Fashion > コンソール使用ガイド

コンソールではサービスを作成したり削除することができ、インデックス状況および検索統計モニタリングのためのダッシュボードを提供します。
AI Fashionのカメラ検索、類似画像商品の推薦機能はAPIで提供されます。[サービスAPIガイド](./service-api-guide)を参考にして実装できます。

コンソールの利用方法は次のとおりです。

## サービスリスト

### サービス作成
![create](http://static.toastoven.net/prod_ai_fashion/console_guide/create_service_ja.png)
1. **サービス作成**をクリックします。
2. サービス名を入力します。
    - サービス名には英小文字、数字、\_(アンダースコア)、-(ハイフン)のみ使用できます。
    - 最初の文字に数字、\_(アンダースコア)、-(ハイフン)は使用できません。
    - 2文字以上、32文字以下でのみ入力できます。
3. **保存**をクリックします。

### サービス削除
![delete](http://static.toastoven.net/prod_ai_fashion/console_guide/delete_service_ja.png)
1. サービスリストから削除したいサービスの**削除**ボタンをクリックします。
    - インデックスが進行中のサービスは削除できません。
2. **削除**ダイアログボックスが表示されたら**確認**ボタンをクリックします。

## インデックスの状況と検索統計を確認

### インデックス状況
サービスリストから選択したサービスのインデックス状況を確認できます。
![indexStatus](http://static.toastoven.net/prod_ai_fashion/console_guide/product_indexing_ja.png)
- 最近のログ順に30個まで表示されます。

### 検索統計
**検索統計**タブをクリックすると、カメラ検索と類似画像商品推薦、ディープタギングのQC(Query Count、APIを呼び出した回数)をチャートで確認できます。
![searchStats](http://static.toastoven.net/prod_ai_fashion/console_guide/statistics_ja.png)
- 前日から最近3か月間の検索QCを確認できます。
- .xls、.csv、.png、.jpeg形式でファイルをダウンロードできます。
