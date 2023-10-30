# CSSスタイリング
## グローバルスタイリング
app/uiフォルダの中に、global.cssというファイル  
このファイルを使って、アプリケーションのすべてのルートにCSSのルールを追加できる  
CSSのリセットルール、リンクのようなHTML要素のサイト全体のスタイルなど  
```
import "@/app/ui/global.css";

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

## TailwindCSS
Tailwindは、ユーティリティクラスを JSX マークアップに直接素早く記述できるようにすることで、開発プロセスをスピードアップする CSS フレームワーク  
CSSはコンポーネントにスコープされるので、スタイルの衝突や個別のスタイルシートの維持を心配する必要はない
```
<h1 className="text-blue-500">I'm blue!</h1>
```

## `clsx`ライブラリを使ってクラス名を切り替える
状態やその他の条件に基づいて、要素に条件付きでスタイルを設定する必要がある場合がある  
**clsx** は、クラス名を簡単に切り替えることができるライブラリである  
- ステータスを受け付けるInvoiceStatus コンポーネントを作成したいとします。ステータスは`pending` または`paid` です。
- `pending` の場合は緑色にします。`paid` の場合はグレーになります。
```
import clsx from 'clsx';
 
export default function InvoiceStatus({ status }: { status: string }) {
  return (
    <span
      className={clsx(
        'inline-flex items-center rounded-full px-2 py-1 text-sm',
        {
          'bg-gray-100 text-gray-500': status === 'pending',
          'bg-green-500 text-white': status === 'paid',
        },
      )}
    >
    // ...
)}
```
