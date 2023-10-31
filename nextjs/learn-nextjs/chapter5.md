# ページ間の移動

## <Link>コンポーネント
Next.jsでは、アプリケーションのページ間をリンクするためにLinkコンポーネントを使うことができる  
アプリケーションの一部はサーバー上でレンダリングされますが、ナビゲーションは高速になり、ページ全体をリフレッシュすることもありません。
```
<Link
  key={link.name}
  href={link.href}
  className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3"
>
<LinkIcon className="w-6" />
<p className="hidden md:block">{link.name}</p>
</Link>
```

## アクティブリンクの表示
一般的なUIパターンは、ユーザーが現在どのページにいるのかを示すために、アクティブなリンクを表示する
URLからユーザーの現在のパスを取得するために`usePathname()`hookを使う  
  
usePathname()はフックなので、nav-links.tsxをクライアント・コンポーネントにする必要がある
Reactの"`se client`ディレクティブをファイルの先頭に追加（Reactのフックは通常、関数コンポーネント内でのみ使われ）
```
"use client";

import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from "@heroicons/react/24/outline";
import Link from "next/link";
import { usePathname } from "next/navigation";
import clsx from "clsx";
```
次に、コンポーネント内のpathnameという変数にパスを代入する：
```
'use client';
 
import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';
import { usePathname } from 'next/navigation';
 
// ...
 
export default function NavLinks() {
  const pathname = usePathname();
  // ...
}
```
clsxで、link.hrefが パス名と一致したときに条件付きでクラス名を適用することができる
```
'use client';
 
import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from '@heroicons/react/24/outline';
import Link from 'next/link';
import { usePathname } from 'next/navigation';
import clsx from 'clsx';
 
// ...
 
export default function NavLinks() {
  const pathname = usePathname();
 
  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <Link
            key={link.name}
            href={link.href}
            className={clsx(
              'flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3',
              {
                'bg-sky-100 text-blue-600': pathname === link.href,
              },
            )}
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </Link>
        );
      })}
    </>
  );
}
```

## 自動コード分割とプリフェッチ
- 自動的なコード分割:
  - Next.jsはルートのセグメントごとに自動的にアプリケーションのコードを分割します。
    - 例えば、/about と /contact という2つの異なるルートがある場合、ユーザーが /about ページにアクセスした時、Next.jsは/aboutページに必要なJavaScriptコードだけをダウンロードします。
    - /contact に関するコードは、そのページに遷移しない限りダウンロードされません。
  - これは、初回のロードで全てのコードを読み込む従来のSPA（シングルページアプリケーション）とは異なります。

- ページの隔離:
  - コードをルートごとに分割することで、各ページが独立します。
  - あるページでエラーが発生しても、アプリケーション全体には影響を与えません。

- コードのプリフェッチ:
  - 本番環境では、<Link>コンポーネントがブラウザのビューポートに表示されると、Next.jsはそのリンク先のルートのコードを自動的にバックグラウンドでプリフェッチ（先読み）します。
  - その結果、ユーザーがリンクをクリックした時には、目的地のページのコードはすでにバックグラウンドで読み込まれており、ページ遷移がほぼ瞬時に行われます。

