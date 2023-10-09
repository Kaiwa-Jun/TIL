## Props/Stateとは
Props：コンポーネントにパラメータを渡すための引数。親→子で値を受け取る際の窓口</br>
State：コンポーネント内の状態を表す変数

## Propsの基本

```
// MyHello.js
export default function MyHello(props) {
  return (
    <div>こんにちは、{props.myName}さん！</div>
  )
}
```
`(props)`で引数として、index.jsで設定した`myName="鈴木"`を受け取っている。</br>
`{props.myName}`として、myNameを表示

```
// index.js
import MyHello from './chap03/MyHello'

root.render(
  <MyHello myName="鈴木" />
)
```
`<MyHello myName="鈴木" />`でMyHelloコンポーネントを呼び出している。</br>
`<MyHello myName="鈴木" />`の他に`<MyHello myName="田中" />`など、引数を変更してコンポーネントの再利用ができる

</br>

```
// MyHello.js
export default function MyHello{ myName } {
  return (
    <div>こんにちは、{ myName }さん！</div>
  )
}
```
Propsでわたす引数が多い場合、{}での分割代入を使う


## イベント処理の基本
イベントによって呼び出されるコード（関数）を**イベントハンドラー**という

## Stateの基本
```
import { useState } from 'react';

// 親のindex.jsからinitをPropsとして受け取っている（init={0}なので初期値0として受け取る）
export default function StateBasic({ init }) {

  // useStateフックを呼び出し、初期値としてinitを渡している
  // countという名前の状態変数と、setCountという名前の状態更新関数が作成される
  const [count, setCount] = useState(init);

  console.log(`count is ${count}.`);

  // setCount関数を使ってcountの値をインクリメント
  const handleClick = () => setCount(count + 1);

  return (
    <>
      <button onClick={handleClick}>カウント</button>
      <p>{count}回、クリックされました。</p>
    </>
  );
}
```
useStateを使うことで、コンポーネント内で動的なデータを簡単に管理できます。</br>
ボタンがクリックされるたびに、setCount関数が呼び出され、countの値が更新され、コンポーネントが再レンダリングされます。</br>
これにより、ユーザーに現在のカウント値が表示されます。</br>

## 条件分岐と繰り返し処理
```
// ForList.js
import React from 'react';

// 書籍情報をindex.jsのProps（src）経由でbooks.jsから取得
export default function ForList({ src }) {
  return (
  <dl>
    // 書籍情報(src)をmapで表示
    {src.map(elem => (
      <React.Fragment key={elem.isbn}>
        <dt>
          <a href={`https://wings.msn.to/books/${elem.isbn}/${elem.isbn}.jpg`}>
              {elem.title}（{elem.price}円）
          </a>
        </dt>
        <dd>{elem.summary}</dd>
      </React.Fragment>
    ))}
  </dl>
  );
}
```

```
// books.js
const books = [
  {
    isbn: '978-4-8156-1336-5',
    title: 'これからはじめるVue.js 3実践入門',
    price: 3740,
    summary: 'JavaScriptフレームワーク「Vue.js」について解説した入門書です。',
    download: true,
  },
  {
    isbn: '978-4-297-13288-0',
    title: '改訂3版JavaScript本格入門',
    price: 3520,
    summary: 'ECMAScript 2022に対応した内容で約200ページ増の大幅改訂。最新の基本文法から、開発に欠かせない応用トピックまで解説します。',
    download: true,
  },
];
export default books;
```

```
// index.js
import MyHello from './chap03/MyHello'

root.render(
  <ForList src={books} />
)
```

## Props/Stateの理解を深める
### コンポーネント配下のコンテンツをテンプレートに反映する

childrenプロップは、コンポーネントが他のコンポーネントや要素を含めることを可能にし、その内容を動的に変更するのに非常に役立ちます。これにより、コンポーネントは再利用可能で柔軟になり、さまざまなシナリオで使用できます。

コード例:

親コンポーネント (index.js):
```
// index.js
root.render(
  <StyledPanel>
    <p>メンバー募集中！</p>
    <p>ようこそ、WINGSプロジェクトへ！！</p>
  </StyledPanel>
);
```
子コンポーネント (StyledPanel.js):
```
// StyledPanel.js
export default function StyledPanel({ children }) {
  return (
    <div style={{...}}>
      {children}
    </div>
  );
}
```
解説:

- StyledPanelコンポーネントはchildrenプロップを受け取り、そのプロップを利用して受け取ったコンテンツをレンダリングします。
- index.jsでは、StyledPanelコンポーネントの開始タグと終了タグの間に2つの`<p>`要素を配置しています。これらの要素はStyledPanelコンポーネントにchildrenとして渡されます。
- StyledPanelコンポーネントはこれらのchildrenをレンダリングし、結果としてスタイルが適用された`<div>`要素の中に2つの`<p>`要素が表示されます。
- この方法により、StyledPanelコンポーネントは再利用可能で、それを利用するコンポーネントごとに異なるコンテンツを含めることができます。また、コンテンツは動的に変更され、異なるシナリオで同じStyledPanelコンポーネントを利用できます。


### （例）Modalコンポーネント：ConfirmDelete.jsやUserInfo.jsでModalのchildrenとして異なるコンテンツを渡している
```
// Modal.js
export default function Modal({ children }) {
  return (
    <div style={{...}}>
      <div style={{...}}>
        {children}
      </div>
    </div>
  );
}

// ConfirmDelete.js
import Modal from './Modal';
function ConfirmDelete() {
  return (
    <Modal>
      <p>本当にこのアイテムを削除しますか？</p>
    </Modal>
  );
}

// UserInfo.js
import Modal from './Modal';
function UserInfo() {
  return (
    <Modal>
      <p>名前: 鈴木</p>
    </Modal>
  );
}

// index.js
import ConfirmDelete from './ConfirmDelete';
import UserInfo from './UserInfo';
ReactDOM.render(<ConfirmDelete />, document.getElementById('root'));
ReactDOM.render(<UserInfo />, document.getElementById('root'));
```
- Modalコンポーネントはchildrenプロップを利用して内容を動的に表示します。（ConfirmDelete.jsやUserInfo.jsのpタグ）
- ConfirmDeleteとUserInfoはModalコンポーネントを利用し、異なる内容を表示します。それぞれがModalのchildrenとして異なるコンテンツを渡しています。
- これにより、Modalコンポーネントを再利用し、異なるコンテンツを動的に表示することが可能になります。

### 複数のchildrenを引き渡す
`Props`を利用して、`<TiledPanel>`要素にtitle/bodyのように複数の属性を渡すようにする
```
// TiledPanel.js
export default function TitledPanel({ title, body }) {
  return (
    <div style={{
      margin: 50,
      padding: 5,
      border: '1px solid #000',
      width: 'fit-content',
      boxShadow: '10px 5px 5px #999',
      backgroundColor: '#fff'
    }}>
      {title}
      <hr />
      {body}
      // {children}が{title}<hr />{body}になっている
    </div>
  );
}
```

```
// index.js
root.render(
  <TitledPanel
    title={<p>メンバー募集中！</p>}
    body={<p>ようこそ、WINGSプロジェクトへ！！</p>}
  ></TitledPanel>
);
```

### 属性値を変数に切り出した例
```
// index.js
const title = <p>メンバー募集中！</p>;
const body = <p>ようこそ、WINGSプロジェクトへ！！</p>;
root.render(
  <TitledPanel title={title} body={body}></TitledPanel>
);
```
