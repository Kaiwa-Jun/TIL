# Static and Dynamic Rendering
## 静的レンダリングとは？
静的レンダリングでは、データの取得とレンダリングは、ビルド時（デプロイ時）または再検証時にサーバー上で行われる  
ユーザーがアプリケーションにアクセスするたびに、キャッシュされた結果が提供される
- **より速いウェブサイト**- プリレンダリングされたコンテンツはキャッシュすることができる
- **サーバー負荷の軽減**- コンテンツがキャッシュされるため、サーバーはユーザーのリクエストごとにコンテンツを動的に生成する必要がない
- **SEO**- プリレンダリングされたコンテンツは、ページが読み込まれた時点ですでに利用可能な状態になっているため、検索エンジンのクローラーがインデックスしやすくなる。これは、検索エンジンのランキング向上につながる

## 動的レンダリングとは？
ダイナミックレンダリングでは、リクエスト時（ユーザーがページにアクセスした時）に、各ユーザーに対してサーバー上でコンテンツがレンダリングされる
- **リアルタイムデータ**- ダイナミックレンダリングにより、アプリケーションはリアルタイムまたは頻繁に更新されるデータを表示できます。これは、データが頻繁に変更されるアプリケーションに最適です。
- **ユーザー固有のコンテンツ**- パーソナライズされたダッシュボードやユーザープロファイルなどのユーザー固有のコンテンツは、ユーザーとの対話に基づいてデータが更新されるため、動的レンダリングによって簡単に提供できます。
- **リクエスト時の情報**- 動的レンダリングでは、クッキーやURL検索パラメータなど、リクエスト時にしかわからない情報にアクセスできます。
