# 検索とページネーションの追加
## スターティングコード
```
import Pagination from '@/app/ui/invoices/pagination';
import Search from '@/app/ui/search';
import Table from '@/app/ui/invoices/table';
import { CreateInvoice } from '@/app/ui/invoices/buttons';
import { lusitana } from '@/app/ui/fonts';
import { InvoicesTableSkeleton } from '@/app/ui/skeletons';
import { Suspense } from 'react';
 
export default async function Page() {
  return (
    <div className="w-full">
      <div className="flex w-full items-center justify-between">
        <h1 className={`${lusitana.className} text-2xl`}>Invoices</h1>
      </div>
      <div className="mt-4 flex items-center justify-between gap-2 md:mt-8">
        <Search placeholder="Search invoices..." />
        <CreateInvoice />
      </div>
      {/*  <Suspense key={query + currentPage} fallback={<InvoicesTableSkeleton />}>
        <Table query={query} currentPage={currentPage} />
      </Suspense> */}
      <div className="mt-5 flex w-full justify-center">
        {/* <Pagination totalPages={totalPages} /> */}
      </div>
    </div>
  );
}
```

1. `<Search />`では、特定の請求書を検索することができます。
2. `<Pagenation />`により、ユーザーは請求書のページ間を移動することができます。
3. `<Table />`は請求書を表示します。
検索機能はクライアントとサーバにまたがります。  
ユーザーがクライアントで請求書を検索すると、URLのパラメータが更新され、サーバーでデータが取得され、新しいデータでテーブルがサーバーで再レンダリングされます。

## なぜURL検索パラメータを使うのか？
### URLパラメータを使って検索を実装する利点
- ブックマークおよび共有可能なURL：検索パラメータはURL内にあるため、ユーザーは検索クエリやフィルタを含むアプリケーションの現在の状態をブックマークし、将来の参照や共有に利用できる。
- サーバーサイド・レンダリングと初期ロード：初期状態をレンダリングするために、URLパラメータをサーバー上で直接消費することができ、サーバーレンダリングの処理が容易になります。
- アナリティクスとトラッキング：検索クエリやフィルタをURLに直接記述することで、クライアントサイドのロジックを追加することなく、ユーザーの行動をトラッキングしやすくなります。

## 検索機能の追加
検索機能を実装するために使用するNext.jsクライアントフックは3つ
- **useSearchParams**- 現在のURLのパラメータにアクセスできるようにします。例えば、このURL/dashboard/invoices?page=1&query=pendingの検索パラメータは以下のようになります：{page：'1', query：pending'}となります。
- **usePathname**- 現在のURLのパス名を読み取ります。例えば、ルート/dashboard/invoices の場合、usePathnameは'/dashboard/invoices' を返します。
- **useRouter**- クライアント・コンポーネント内のルート間のナビゲーションをプログラムで有効にします。使用できるメソッドは複数あります。
  
導入ステップは以下
1. ユーザーの入力をキャプチャする。
2. 検索パラメータでURLを更新する。
3. URLを入力フィールドと同期させておく。
4. 検索クエリを反映させるためにテーブルを更新する。

### 1. ユーザーの入力をキャプチャする
- `use client`- クライアント・コンポーネントで、イベント・リスナーやフックを使えるようにする
- `<input>`- これは検索入力欄を設ける
```
'use client';
 
import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
 
export default function Search({ placeholder }: { placeholder: string }) {
  function handleSearch(term: string) {
    console.log(term);
  }
 
  return (
    <div className="relative flex flex-1 flex-shrink-0">
      <label htmlFor="search" className="sr-only">
        Search
      </label>
      <input
        className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
        placeholder={placeholder}
        onChange={(e) => {
          handleSearch(e.target.value);
        }}
      />
      <MagnifyingGlassIcon className="absolute left-3 top-1/2 h-[18px] w-[18px] -translate-y-1/2 text-gray-500 peer-focus:text-gray-900" />
    </div>
  );
}
```
handleSearch関数を作成し、<input>要素にonChangeリスナーを追加する  
onChangeは、入力値が変わるたびにhandleSearchを呼び出す

### 2. 検索パラメータでURLを更新する。
```
'use client';
 
import { MagnifyingGlassIcon } from '@heroicons/react/24/outline';
import { useSearchParams, usePathname, useRouter } from 'next/navigation';
 
export default function Search() {
  const searchParams = useSearchParams();
  const pathname = usePathname();
  const { replace } = useRouter();
 
  function handleSearch(term: string) {
    const params = new URLSearchParams(searchParams);
    if (term) {
      params.set('query', term);
    } else {
      params.delete('query');
    }
    replace(`${pathname}?${params.toString()}`);
  }
}
```
1. 変数の使用：
${pathname}は現在のページのパスを表しており、例として"/dashboard/invoices"が使用されています。

2. 検索機能の実装：
ユーザーが検索バーに入力すると、params.toString()はその入力をURLで使える形式に変換します。

3. URLの更新：
replace(${pathname}?${params.toString()})はユーザーの検索データを含んだ新しいURLに更新します。たとえばユーザーが"lee"と検索すると、URLは/dashboard/invoices?query=leeと更新されます。

4. ページのリロードなしでのURL更新：
Next.jsのクライアントサイドナビゲーションにより、ページをリロードすることなくURLが更新されます。これについては、ページ間のナビゲーションに関する章で学んだ内容です。

### 3. URLを入力フィールドと同期させておく。
```
<input
  className="peer block w-full rounded-md border border-gray-200 py-[9px] pl-10 text-sm outline-2 placeholder:text-gray-500"
  placeholder={placeholder}
  onChange={(e) => {
    handleSearch(e.target.value);
  }}
  defaultValue={searchParams.get('query')?.toString()}
/>
```
1. デフォルト値の設定:
検索入力フィールドにdefaultValueプロパティを使い、searchParams.get('query')から取得した値を初期値として設定しています。
  
2. URLパラメータの使用:
URLに含まれるクエリパラメータqueryの値を読み込んで、検索入力フィールドのデフォルト値としています。これにより、ページを共有した際やリンク経由でアクセスした際に、その検索条件が入力フィールドに既に入力されている状態になります。
  
3. onChangeイベントハンドラ:
ユーザーが入力フィールドに入力するたびに、onChangeイベントがトリガーされ、handleSearch関数がその入力値を処理するようになっています。

### 4. 検索クエリを反映させるためにテーブルを更新する。
最後に、検索クエリを反映させるためにテーブル・コンポーネントを更新する必要がある  
pageコンポーネントは `searchParams`というpropを受け取るので、現在のURLパラメータを<Table>コンポーネントに渡すことができ
```
import Pagination from '@/app/ui/invoices/pagination';
import Search from '@/app/ui/search';
import Table from '@/app/ui/invoices/table';
import { CreateInvoice } from '@/app/ui/invoices/buttons';
import { lusitana } from '@/app/ui/fonts';
import { Suspense } from 'react';
import { InvoicesTableSkeleton } from '@/app/ui/skeletons';
 
export default async function Page({
  searchParams,
}: {
  searchParams?: {
    query?: string;
    page?: string;
  };
}) {
  const query = searchParams?.query || '';
  const currentPage = Number(searchParams?.page) || 1;
 
  return (
    <div className="w-full">
      <div className="flex w-full items-center justify-between">
        <h1 className={`${lusitana.className} text-2xl`}>Invoices</h1>
      </div>
      <div className="mt-4 flex items-center justify-between gap-2 md:mt-8">
        <Search placeholder="Search invoices..." />
        <CreateInvoice />
      </div>
      <Suspense key={query + currentPage} fallback={<InvoicesTableSkeleton />}>
        <Table query={query} currentPage={currentPage} />
      </Suspense>
      <div className="mt-5 flex w-full justify-center">
        {/* <Pagination totalPages={totalPages} /> */}
      </div>
    </div>
  );
}
```
`<Table>`コンポーネントに移動すると、queryと currentPageの2つのpropsがfetchFilteredInvoices()関数に渡され、クエリに一致する請求書を返す
```
// ...
export default async function InvoicesTable({
  query,
  currentPage,
}: {
  query: string;
  currentPage: number;
}) {
  const invoices = await fetchFilteredInvoices(query, currentPage);
  // ...
}
```

## Best practice: Debouncing
- デバウンスは、関数が実行される頻度を制限するプログラミングのベストプラクティス  
- 検索機能を実装する際に、ユーザーがキーボードを操作するたびにデータベースに問い合わせを送ると、大量の不要なリクエストが生じ、システムに負荷をかける可能性がある
```
// ...
import { useDebouncedCallback } from 'use-debounce';
 
// Inside the Search Component...
const handleSearch = useDebouncedCallback((term) => {
  console.log(`Searching... ${term}`);
 
  const params = new URLSearchParams(searchParams);
  if (term) {
    params.set('query', term);
  } else {
    params.delete('query');
  }
  replace(`${pathname}?${params.toString()}`);
}, 300);
```
1. `handleSearch` 関数に `console.log` を追加して、ユーザーが検索バーに入力するたびに "Searching..." と表示させる。
2. 検索バーに "Emil" と入力すると、各キーストロークで "Searching..." とログが表示されることが確認できる。
3. `use-debounce` ライブラリをインストールして、デバウンス機能を実装する。
4. `useDebouncedCallback` 関数をインポートして、`handleSearch` をラップし、ユーザーが入力を停止してから300ミリ秒後にコードが実行されるようにする。
  
デバウンスを適用することで、ユーザーがタイピングを止めてから300ミリ秒後にのみ検索が実行され、システムへの不要なリクエストを減らし、パフォーマンスを向上させることができる

## paginationの追加
