# データの取得
## データの取得方法の選択
### API
Next.jsでは、ルートハンドラを使ってAPIエンドポイントを作成できる

### データクエリ
フルスタックのアプリケーションを作成する場合、データベースとやりとりするためのロジックを書く必要もある  Postgresのようなリレーショナルデータベースの場合、SQLやPrisma のようなORMを使ってこれを行うことができる
  
- APIエンドポイントを作成する際には、データベースとやり取りするためのロジックを記述する必要がある
- React Server Components（サーバー上でデータを取得する）を使用している場合は、APIレイヤーをスキップして、データベースの秘密をクライアントに公開するリスクを冒すことなく、データベースに直接問い合わせることができる

## サーバーコンポーネントを使用してデータを取得する
デフォルトで、Next.jsアプリケーションは`React Server Components`を使用        
必要に応じて`Client Components`を選択できる  
`React Server Components`でデータを取得すると、いくつかの利点がある：
- Server Componentsはサーバー上で実行されるため、高価なデータ取得やロジックをサーバー上に保持し、その結果のみをクライアントに送信することができる
- Server Componentsはpromiseをサポートし、データフェッチなどの非同期タスクをよりシンプルに解決します。useEffectや useState、データ取得ライブラリに手を伸ばすことなく、async/await構文を使用できる
- Server Componentsはサーバー上で実行されるため、APIレイヤーを追加することなく、データベースに直接問い合わせることができる

## SQLの使用
今回のdashboardプロジェクトでは、`Vercel Postgres SDK`と SQL を使用してデータベース・クエリを記述します。  
### SQLを使う理由：
- SQLは、リレーショナルデータベースをクエリするための業界標準である（例えば、ORMはSQLを生成する）。
- SQLの基本を理解することで、リレーショナル・データベースの基本を理解することができ、その知識を他のツールに応用することができる。
- SQLは汎用性が高く、特定のデータを取得したり操作したりすることができる。
- Vercel Postgres SDK はSQL インジェクションからの保護を提供します

## request waterfallsとは？
前のリクエストの完了に依存する一連のネットワーク・リクエストのこと  
データ・フェッチの場合、各リクエストは、前のリクエストがデータを返して初めて開始できる
```
const revenue = await fetchRevenue();
const latestInvoices = await fetchLatestInvoices(); // fetchRevenue() の終了を待つ
const {
  numberOfInvoices,
  numberOfCustomers,
  totalPaidInvoices,
  totalPendingInvoices,
} = await fetchCardData(); // fetchLatestInvoices() の終了を待つ
```

## Parallel data fetching
JavaScriptでは、Promise.all()、またはPromise.allSettled()関数を使うと、すべてのプロミスを同時に開始することができる
```
export async function fetchCardData() {
  try {
    const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`;
    const customerCountPromise = sql`SELECT COUNT(*) FROM customers`;
    const invoiceStatusPromise = sql`SELECT
         SUM(CASE WHEN status = 'paid' THEN amount ELSE 0 END) AS "paid",
         SUM(CASE WHEN status = 'pending' THEN amount ELSE 0 END) AS "pending"
         FROM invoices`;
 
    const data = await Promise.all([
      invoiceCountPromise,
      customerCountPromise,
      invoiceStatusPromise,
    ]);
    // ...
  }
}
```
- すべてのデータ・フェッチを同時に実行し始めることで、パフォーマンスを向上させることができる。
  - 上記例では、invoiceCountPromise,customerCountPromise,invoiceStatusPromiseそれぞれを並列に処理してその結果をprosise.all()にその結果を配列に返す。
- どんなライブラリやフレームワークにも適用できるネイティブのJavaScriptパターンを使う。
