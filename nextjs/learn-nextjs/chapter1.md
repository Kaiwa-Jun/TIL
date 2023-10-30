# 新規プロジェクトの作成
```
npx create-next-app@latest nextjs-dashboard --use-npm --example "https://github.com/vercel/next-learn/tree/main/dashboard/starter-example"
```
このコマンドは、Next.jsアプリケーションをセットアップするコマンドラインインターフェイス（CLI）ツールであるcreate-next-appを使用します。  
上記のコマンドでは、--exampleフラグもこのコースのスターターサンプルとともに使用しています。
  
インストール後、コードエディタでプロジェクトを開き、nextjs-dashboardに移動
```
cd nextjs-dashboard
```

# フォルダ構造
![フォルダ構造](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Flearn-folder-structure.png&w=3840&q=75&dpl=dpl_GWgyVSdHCrYdH5TfBYmmG7pRiHi8)

- `/app`：アプリケーションのすべてのルート、コンポーネント、ロジックを含みます。
- `/app/lib`：再利用可能なユーティリティ関数やデータ取得関数など、アプリケーションで使用される関数が含まれています。
- `/app/ui`：カード、テーブル、フォームのような、アプリケーションのすべてのUIコンポーネントを含みます。時間を節約するために、これらのコンポーネントはあらかじめスタイル設定されています。
- `/public`：画像など、アプリケーションのすべての静的アセットが含まれます。
- `/scripts/`：後の章でデータベースの入力に使用するファイルが含まれています。
- `設定ファイル`：アプリケーションのルートには、next.config.jsのような設定ファイルもあります。これらのファイルのほとんどは、create-next-appを使用して新しいプロジェクトを開始するときに作成され、事前に設定されます。このコースで修正する必要はありません。

# プレイスホルダー
このプロジェクトでは、app/lib/placeholder-data.jsにいくつかのプレースホルダー・データを用意しました。  
ファイル内の各JavaScriptオブジェクトは、データベースのテーブルを表します。  
例えば、invoicesテーブルの場合：
```
const invoices = [
  {
    customer_id: customers[0].id,
    amount: 15795,
    status: 'pending',
    date: '2022-12-06',
  },
  {
    customer_id: customers[1].id,
    amount: 20348,
    status: 'pending',
    date: '2022-11-14',
  },
  // ...
];
```

# 開発サーバーの実行
`npm i`を実行して、プロジェクトのパッケージをインストールする
```
npm i
```
npm run devで開発サーバーを起動する。
```
npm run dev
```
