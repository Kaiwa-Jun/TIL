## 2種類のルーター
Next.jsでのアプリのパスを管理するためのルーター
- Pages Router
- App Router(Next.js13から)

## App Routerとは
### AppRouter
- 位置: 通常、プロジェクトのsrcフォルダ内のrouter、routes、またはconfigディレクトリなどに配置されます。

- 内容: ルーティング全体の設定、サブルーティング、認証ガード（ログインが必要かどうか等）などが記述されます。

- ファイル構造の例:
```
src/
├── /app
│   └── page.js
│   ├── /about
│   │    ├── page.js
│   │    └── layout.js
│   │
│   └── /article/[id]
│        └── page.js
```

### Pages Router
- 位置: Next.jsのようなフレームワークでは、pagesディレクトリがこの役割を果たします。

- 内容: 個々のページに関するルーティング設定が自動的に行われます。ファイル名やフォルダ構造に基づいてルーティングが決まります。

- ファイル構造の例:
```
src/
├── pages/
│   ├── index.js
│   ├── about.js
│   └── articles/
│       └── [id].js
```

#### App Routerにおける予約ファイル
| ファイル名 | 概要 |
|:---|:---:|
|layout.js |レイアウト（ページの枠組み）を生成 |
|page.js |ページの中核 |
|loading.js |ローディングUIを生成 |
|error.js |エラーページを生成 |
|not-found.js |エラーページを生成（Not Found時） |

## プロジェクト規定のサンプルを確認する
```
// src/app/layout.js
import Link from 'next/link';
import './globals.css';
import { Inconsolata } from 'next/font/google';

const fnt = Inconsolata({ subsets: ['latin'] })

//メタ情報の設定
export const metadata = {
  title: 'Reading Recorder',
  description: '自分が読んだ書籍の記録を残すためのアプリ',
};

// ルートレイアウトの準備
export default function RootLayout({ children }) {
  return (

    //①
    <html lang="ja">
      <body className={fnt.className}>
      <h1 className="text-4xl text-indigo-800 font-bold my-2">
        Reading Recorder</h1>
      <ul className="flex bg-blue-600 mb-4 pl-2">
        <li className="block px-4 py-2 my-1 hover:bg-gray-100 rounded">
          <Link className="no-underline text-blue-300" href="/">
            Home</Link></li>
      </ul>
      <div className="ml-2">

        //②
        {children}
      </div>
      </body>
    </html>
  );
}
```
①`<html>`、`<body>`要素が存在する  
②個々のページを埋め込むために、childrenプロパティを引用している

## App Routerのルートパラメーター
| フォルダ階層 | 対応するリクエスト | パラメータ |
|:---|:---|:---|
|app/notes/[id]/page.js |/notes/108 |{id:'108'} |
|app/search/[...keywords]/page.js |/search/react/next |{keywords:['react','next']} |
| app/search/[[...keywords]]/page.js | /search            | `{}`                        |
| app/search/[[...keywords]]/page.js | /search/react/next | `{keywords:['react','next']}` |
