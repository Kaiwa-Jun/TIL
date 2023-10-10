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



