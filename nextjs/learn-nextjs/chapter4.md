# レイアウトとページの作成
## ネストされたルーティング
- Next.jsでは、ネストされたルートを作成するためにフォルダが使用されるファイルシステムルーティングが使用される
- 各フォルダはURLセグメントにマッピングされたルートセグメントを表す
![ネストされたルーティング](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Ffolders-to-url-segments.png&w=3840&q=75&dpl=dpl_GWgyVSdHCrYdH5TfBYmmG7pRiHi8)

  
- /app/login/page.tsxは /loginパスに関連付けられ
![ネストされたルーティング](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Flogin-route.png&w=3840&q=75&dpl=dpl_GWgyVSdHCrYdH5TfBYmmG7pRiHi8)

## ダッシュボードページの作成

```
export default function Page() {
  return <p>Dashboard Page</p>;
}
```
1. appの中にdashboardという新しいフォルダを作成
2. dashboardフォルダの中に、page.tsxファイルを作成

## ダッシュボードレイアウトの作成
```
import SideNav from "@/app/ui/dashboard/sidenav";

export default function Layout({ children }: { children: React.ReactNode }) {
  return (
    <div className="flex h-screen flex-col md:flex-row md:overflow-hidden">
      <div className="w-full flex-none md:w-64">
        <SideNav />
      </div>
      <div className="flex-grow p-6 md:overflow-y-auto md:p-12">{children}</div>
    </div>
  );
}
```
![ネストされたルーティング](https://nextjs.org/_next/image?url=%2Flearn%2Fdark%2Fshared-layout.png&w=3840&q=75&dpl=dpl_GWgyVSdHCrYdH5TfBYmmG7pRiHi8)

- `<SideNav />`コンポーネントをレイアウトにインポート
- このファイルにインポートしたコンポーネントは、すべてレイアウトの一部になる
- Layoutコンポーネントはchildrenプロップを受け取る
- 子はページか別のレイアウトになる（/dashboardの中のページは自動的にLayoutの中にネストされる）
### layoutを使用するメリット
- ナビゲーション時にページコンポーネントだけが更新され、レイアウトは再レンダリングされない
例えば、/dashboard/settingsと /dashboard/analyticsの2つの兄弟ルート間を移動する場合、settingsと analyticsページがレンダリングされ、共有ダッシュボードのレイアウトは保持される（再レンダリングしない）

## ルートレイアウト
/app/layout.tsx
```
import '@/app/ui/global.css';
import { inter } from '@/app/ui/fonts';
 
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={`${inter.className} antialiased`}>{children}</body>
    </html>
  );
}
```
- ルートレイアウトに追加したUIはアプリケーションのすべてのページで共有される
- ルートレイアウトを使って<html>タグと<body>タグを修正し、メタデータを追加できる
