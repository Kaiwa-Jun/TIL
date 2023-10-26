## 複数のStateに応じてページを制御する例
```
//HookTransition.js
import { useState, useTransition } from 'react';
import books from './books';
import commentList from './comments';
import { BookDetails, CommentList } from './HookTransitionChild';

export default function HookTransition() {
  const [isbn, setIsbn] = useState('');
  const [comments, setComments] = useState([]);
  const [isPending, startTransition] = useTransition();

  const handleChange = e => {
    const isbn = e.target.value;
    setIsbn(isbn);
    setComments(commentList.filter(c => c.isbn === isbn));

    // startTransition(() => {
    //   setComments(commentList.filter(c => c.isbn === isbn));
    // });
  };

  return (
      <>
      <select onChange={handleChange}>
        <option value="">選択してください。</option>
        {books.map(b => (
           <option key={b.isbn} value={b.isbn}>{b.title}</option>
        ))}
      </select>
      <BookDetails isbn={isbn} />
      <hr />
      <CommentList src={comments}
        // isPending={isPending}
      />
      </>
  );
}
```
```
//HookTransitionChild.js
import { memo } from 'react';
import books from './books';

const sleep = (delay) => {
  const start = Date.now();
  while (Date.now() - start < delay);
};

export function BookDetails({ isbn }) {
  const book = books.find(b => b.isbn === isbn);
  return (
    <ul>
      <li>ISBN：{book?.isbn}</li>
      <li>書名：{book?.title}</li>
      <li>価格：{book?.price}円</li>
      <li>概要：{book?.summary}</li>
      <li>配布サンプル：{(book?.download) ? 'あり': 'なし'}</li>
    </ul>
  );
}

export const CommentList = memo(function({ src, isPending }){
  if (isPending) return <p>Now Loading...</p>;
  return (
    <ol>
      {src.map(c => <CommentItem key={c.id} src={c} />)}
    </ol>
  );
});

function CommentItem({ src }) {
  sleep(1000);
  return <li>{src.body}（{src.rank}）</li>;
}
```
1. `<HookTransition />` コンポーネントがレンダリングされます。
2. `<select>` 要素で選択が変更されると、handleChange 関数が呼び出されます。
3. handleChange 関数内で、選択された書籍のISBNに基づいてコメントをフィルタリングし、状態を更新します。
4. 状態が更新されると、`<BookDetails />` と `<CommentList />` も再レンダリングされます。

### 問題点
- CommentItem コンポーネント内で sleep(1000); が呼ばれています。
- これは1秒間処理をブロックするため、他の要素（例えば、`<BookDetails />`）が描画されるまでに遅延が発生します。

## useTransition関数による描画の優先順位付け
特定のStateをトランジションにマークすると優先度の高い（isbn）を先に反映して、優先度の低い更新（cinnebts）を後回しにできる
```
export default function HookTransition() {
  const [isbn, setIsbn] = useState("");
  const [comments, setComments] = useState([]);

  //②トランジションを利用するための準備
  const [isPending, startTransition] = useTransition();

  const handleChange = (e) => {
    const isbn = e.target.value;
    setIsbn(isbn);

    //①トランジションの配下でStateを更新する
    startTransition(() => {
      setComments(commentList.filter((c) => c.isbn === isbn));
    });
  };
```
①即座に更新しなくていいStateをstartTransitionで括る  
②startTransition関数を作成するのがuseTransition関数

## トランジションの状態に応じて処理を振り分ける
```
  return (
    <>
      <select onChange={handleChange}>
        <option value="">選択してください。</option>
        {books.map((b) => (
          <option key={b.isbn} value={b.isbn}>
            {b.title}
          </option>
        ))}
      </select>
      <BookDetails isbn={isbn} />
      <hr />
      <CommentList src={comments} isPending={isPending} />
    </>
  );
}
```
**isPending**
- useTransitionの戻り値
- トランジションによる反映が保留（ペンディング）になっているかを表すブール値
- CommentListコンポーネントに対して、Props経由でisPendingの値を渡す
- その値によってローディングメッセージを表示


