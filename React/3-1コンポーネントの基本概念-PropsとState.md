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

### childrenから目的の要素を取り出す
childrenからkey属性をキーにして、目的の要素を取り出せる
```
// TitlePanel.js
export default function TitledPanel({ children }) {
  const title = children.find(elem => elem.key === 'title');
  const body = children.find(elem => elem.key === 'body')

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
    </div>
  );
}
```

```
// index.js
root.render(
  <TitledPanel>
    <p key="title">メンバー募集中！</p>
    <p key="body">ようこそ、WINGSプロジェクトへ！！</p>
  </TitledPanel>
);
```
- TitledPanelコンポーネントはchildrenプロップを受け取ります。
- childrenの中からkeyプロパティが'title'、'body'となっている要素を検索し、title変数とbody変数に格納します。
- TitledPanelコンポーネントは、title変数とbody変数の内容をレンダリングし、タイトル部分とボディ部分を表示します。
- この方法により、TitledPanelコンポーネントを利用するコンポーネント（この例ではindex.js）で異なるタイトルとボディの内容を動的に表示することができます。

### childrenに対してパラメータを渡す
```
// ListTemplate.js
export default function ListTemplate({ src, children }) {
  return (
    <dl>
      {src.map(elem => (
        <React.Fragment key={elem.isbn}>
          {/* {children} */}
          {children(elem)}
        </React.Fragment>
      ))}
    </dl>
  );
}
```
- srcとchildrenをPropsとして受け取っている
- srcは配列で、childrenは関数
- src配列の各要素(elem)に対して、children関数を呼び出し、その結果をレンダリング
- {children(elem)}はchildren関数をelem引数とともに呼び出している(index.jsの{(elem) => }の部分)
```
//index.js
root.render(
  <ListTemplate src={books}>
    {(elem) => (
      <>
        <dt>
          <a href={`https://wings.msn.to/books/${elem.isbn}/${elem.isbn}.jpg`}>
            {elem.title}（{elem.price}円）
          </a>
        </dt>
        <dd>{elem.summary}</dd>
      </>
    )}
  </ListTemplate>
);
```
- ListTemplateコンポーネントを呼び出し、srcプロップにbooks配列を渡している
- childrenプロップとして関数を渡しています。この関数はelemを引数として受け取り、JSX要素を返す
- この関数は、各elemに対して`<dt>`と`<dd>`要素を作成し、elemのプロパティを利用してコンテンツを表示する

### State値更新のための2つの構文
#### １ずつ増加させる
```
// StateBasic.js
import { useState } from "react";

export default function StateBasic({ init }) {
  const [count, setCount] = useState(init);
  console.log(`count is ${count}.`);

  const handleClick = () => {
    setCount(count + 1);
    setCount(count + 1);
  };

  return (
    <>
      <button onClick={handleClick}>カウント</button>
      <p>{count}回、クリックされました。</p>
    </>
  );
}
```
```
// index.js
root.render(<StateBasic init={0} />);
```
- `setCount(count + 1);`が2行あるが値は１ずつ増加
- ReactではStateを非同期に更新している
- イベントハンドラー`const handleClick = () => { … };`が終えた後でcountを更新する
- `setCount(count + 1);`の1行目でカウントアップするが、2行目では同じ値のまま更新されない

#### ２ずつ増加させるには
```
import { useState } from "react";

export default function StateBasic({ init }) {
  const [count, setCount] = useState(init);
  console.log(`count is ${count}.`);

  const handleClick = () => {
    setCount(c => c + 1);
    setCount(c => c + 1);
  };
  return (
    <>
      <button onClick={handleClick}>カウント</button>
      <p>{count}回、クリックされました。</p>
    </>
  );
}
```
- setCount(c => c + 1)→`c`:現在のStateの値、`c+1`:更新のための式
- `c`の部分のState値は、その時々の最新の値であることが保証される

### 子コンポーネントから親コンポーネントへの情報伝達
- 親→子はProps
- 子→親はState
- 子コンポーネントから親コンポーネントのStateを更新することで情報を伝達する

```
// StateParent.js
import { useState } from "react";
import StateCounter from "./StateCounter";

export default function StateParent(init) {
  const [count, setCount] = useState(0);
  const update = (step) => setCount((c) => c + step);
  return (
    <>
      <p>総カウント：{count}</p>
      <StateCounter step={1} onUpdate={update} />
      <StateCounter step={5} onUpdate={update} />
      <StateCounter step={-1} onUpdate={update} />
    </>
  );
}
```
- onUpdate プロパティは、update 関数を参照しています: const update = (step) => setCount((c) => c + step);
- ユーザーがいずれかのボタンをクリックすると、StateCounter.jsのhandleClick 関数が実行され、onUpdate 関数が step 引数とともに呼び出される
- update 関数で、現在の count 値に step 値を加えることで `count` state を更新
- `count` state が更新されると、StateParent コンポーネントが再レンダリングされ、新しい `count` 値が画面に表示されます: <p>総カウント：{count}</p>
```
//StateCounter.js
import './StateCounter.css';

export default function StateCounter({ step, onUpdate }) {
  const handleClick = () => onUpdate(step);
  return (
    <button className="cnt" onClick={handleClick}>
      <span>{step}</span>
    </button>
  );
}
```
- StateCounter コンポーネントは step と onUpdate の2つのプロパティを受け取る
- handleClick 関数を定義し、この関数は onUpdate 関数を step 引数とともに呼び出します: const handleClick = () => onUpdate(step);
- ユーザーがいずれかのボタンをクリックすると、handleClick 関数が実行され、onUpdate 関数が step 引数とともに呼び出される
- この onUpdate 関数は実際には StateParent.js に定義された update 関数で、現在の `count` 値に step 値を加えることで `count` state を更新
```
// index.js
root.render(<StateParent />);
```
