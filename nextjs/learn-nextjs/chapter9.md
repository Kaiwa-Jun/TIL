# ストリーミング
## ストリーミングデータ取得
- コンポーネント単位: 各コンポーネントは独立してデータを取得し、レンダリングします。
- 段階的表示: データが準備できたコンポーネントから順に表示されます。
## 早いレスポンス
- 非ブロッキング: 遅いデータ取得が他の表示を妨げません。
- インタラクティブ: データの準備が整うとすぐにユーザーは対応するUIを見ることができます。
## ウォーターフォール方式との比較
- パラレル処理: データ取得とレンダリングが並行して行われます。
- 非シーケンシャル: 全てのデータが揃うのを待たず、準備ができた部分から表示します。

Next.jsでストリーミングを実装する方法は2つある:  
- ページレベルではloading.tsx
- 特定のコンポーネントについては、<Suspense>

## loading.tsxによるページ全体のストリーミング
- loading.tsxは、Suspenseの上に構築された特別なNext.jsファイルで、ページのコンテンツがロードされる間、代替として表示されるローディングUIを作成することができる
- <Sidebar>は静的なので、すぐに表示されます。動的コンテンツがロードされている間、ユーザーは<Sidebar>とインタラクトすることができる
- ユーザーは、ページの読み込みが終わるまでナビゲーションを待つ必要はない（これを中断可能なナビゲーションと呼ぶ）

## ローディングスケルトンバグの修正について
現在の問題:  
- ローディングスケルトンが「請求書（invoices）」ページと「顧客（customers）」ページにも適用されてしまいます。
- loading.tsx はファイルシステム上で /invoices/page.tsx や /customers/page.tsx よりも上の階層にあるため、これらのページに影響します。
  
解決策: ルートグループを使用する  
- ダッシュボードフォルダ内に /(overview) という新しいフォルダを作成します。
- loading.tsx と page.tsx ファイルをそのフォルダ内に移動します。
  
フォルダ構造:  
- ルートグループを作成するために、括弧 () を使用して新しいフォルダを作ります。
- このフォルダ名はURLのパスには含まれません。そのため、 /dashboard/(overview)/page.tsx のURLは単に /dashboard となります。
  
利点:    
- ルートグループを利用することで、ローディングスケルトンがダッシュボードの概要ページにのみ適用されるようになります。
- ルートグループは、URLのパス構造に影響を与えずに、ファイルを論理的なグループに整理するのに役立ちます。
- また、アプリケーションをセクション（例: (marketing) ルートや (dashboard) ルート）や、大規模なアプリケーションのチームごとに分けるためにも使用できます。

## Streaming a component
1. コンポーネント単位での遅延ロード：
ページ全体ではなく、特定のコンポーネント（例：RevenueChart）のロードをReactのSuspenseを使用して遅延させることができます。

2. Suspenseの導入：  
アプリケーションの一部を、条件（データのロードなど）が満たされるまでレンダリングを遅延させることができます。
遅延ロードするコンポーネントをタグでラップし、データがロードされるまでの間に表示するフォールバックコンポーネント（例：<RevenueChartSkeleton />）を指定します。
  
3. fetchRevenue()のデータ取得の移行：
遅いデータリクエストfetchRevenue()がページのロードを遅らせていたので、このリクエストをページからコンポーネント内へと移動させます。

4. コードの更新：  
/dashboard/(overview)/page.tsxからfetchRevenue()とそのデータを関連するすべてのインスタンスを削除します。
<RevenueChart>コンポーネントを更新して、自身のデータをフェッチするように変更し、以前は親コンポーネントから受け取っていたプロップスを削除します。

5. 結果：  
ページをリフレッシュすると、ダッシュボードの情報がほぼ即座に表示されますが、<RevenueChart>のデータがロードされるまでの間はフォールバックのスケルトンが表示されます。