## 関数コンポーネントで「インスタンス変数」を定義する
### useRef関数を利用しない例
```
import { useState } from 'react';

export default function HookRefNg() {
  let id = null;
  const [count, setCount] = useState(0);

  const handleStart = () => {
    if (id === null) {
      id = setInterval(() => setCount(c => c + 1), 1000);
    }
  };
  const handleEnd = () => {
    clearInterval(id);
    id = null;
  };

  return (
    <>
      <button onClick={handleStart}>開始</button>
      <button onClick={handleEnd}>終了</button>
      <p>{count}秒経過</p>
    </>
  );
}
```
1. コンポーネントの初期化:
- コンポーネントが最初にレンダリングされるとき、useState によって count は0に初期化されます。
- `let id = null;`も初期化されますが、この変数は関数が呼び出される度に再初期化される設計上の問題があります。

2. ボタンクリックで関数実行: 
- "開始"ボタンを押すと handleStart 関数が呼ばれます。
- この時点で id が null なら、setInterval がスタートし、そのIDが id に格納されます。
- この setInterval は1秒ごとに setCount を呼び出し、count の値を1増やします。

3. 状態の更新と再レンダリング: 
- setCount が呼び出されると、count の状態が更新され、コンポーネントが再レンダリングされます。
    
4. "終了"ボタンでタイマー停止:
- "終了"ボタンをクリックするとhandleEndが呼び出されます。
- clearInterval(id)が呼び出されて、タイマーが停止します。
- idがnullにリセットされます。
    
**問題点**
- `let id = null;`が関数（コンポーネント）内のローカル変数であるため、コンポーネントが再レンダリングされても id の値は保持されない
- "終了"ボタンを押しても、id が維持されないためにタイマーが正しく停止できない
- この問題を解消するには、useRef を使って id をコンポーネント間で維持することが推奨される

### useRef関数を利用した例
```
import { useState, useRef } from 'react';

export default function HookRef() {
  const id = useRef(null);
  const [count, setCount] = useState(0);

  const handleStart = () => {
    if (id.current === null) {
      id.current = setInterval(() => setCount(c => c + 1), 1000);
    }
  };
  const handleEnd = () => {
    clearInterval(id.current);
    id.current = null;
  };

  return (
    <>
      <button onClick={handleStart}>開始</button>
      <button onClick={handleEnd}>終了</button>
      <p>{count}秒経過</p>
    </>
  );
}
```
1. コンポーネントの初期化:
- コンポーネントが最初にレンダリングされるとき、useState によって count は0に初期化されます。
- useRef によって id も null で初期化されますが、useRef が返すオブジェクトの .current プロパティはレンダリング間で維持されます。
    
2. "開始"ボタンで関数実行:
- "開始"ボタンを押すと handleStart 関数が呼ばれます。
- id.current が null の場合、setInterval がスタートし、そのIDが id.current に格納されます。
- setInterval関数は、作成したタイマーを識別するIDを返します。
- そのIDをid.currentに格納しています。
- この setInterval は1秒ごとに setCount を呼び出し、count の値を1増やします。
- id.current = setInterval(...)で、新しいタイマーを作って、その「識別ID」をid.currentに格納している
- この「識別ID」は**再レンダリング後も保持され続ける**
    
3. 状態の更新と再レンダリング:
- setCount が呼び出されると、count の状態が更新され、コンポーネントが再レンダリングされます。
- このとき、**id.current の値は維持される**ため、問題がありません。
    
4. "終了"ボタンでタイマー停止:
- "終了"ボタンを押すと handleEnd 関数が呼び出されます。
- clearInterval(id.current) が呼び出され、タイマーが停止します。
- その後、id.current は null にリセットされます。

**useRefを使用することで、同じidを参照（reference）しているイメージ**

### Refをコンポーネントは以下の要素にフォワードする
```
import { useEffect, useRef } from 'react';
import MyTextBox from './MyTextBox';

export default function HookRefForward() {
  const text = useRef(null);
  useEffect(() => {
    text.current?.focus();
  }, []);

  return (
    <MyTextBox label="名前" ref={text} />
  );
}
```
```
import { forwardRef } from "react";

const MyTextBox = forwardRef(({ label }, ref) => (
  <label>
    {label}：
    <input type="text" size="15" ref={ref} />
  </label>
));

export default MyTextBox;
```
1. コンポーネント初期化:    
HookRefForwardコンポーネントが初めてレンダリングされるときに、useRef(null)によってtextという名前のrefオブジェクトが生成されます。

2. リファレンスの転送:    
forwardRefを使用してMyTextBoxコンポーネントが定義されています。これにより、このコンポーネントはrefを内部のDOM要素、ここではinput要素、に転送することができます。

3. 子コンポーネントとref:  
HookRefForwardから`<MyTextBox label="名前" ref={text} />`としてMyTextBoxコンポーネントを呼び出しています。ここで、text refがinput要素に直接渡されます。

4. 自動フォーカス:  
useEffectフックが発火して、その中でtext.current?.focus();が実行されます。これにより、input要素に自動的にフォーカスが当たります。
    
- この構成により、HookRefForwardコンポーネントが表示された際に、MyTextBoxコンポーネント内のinputフィールドに自動的にフォーカスが当たるようになっています。  
- 例えば、検索ページやログインページなどで、ユーザーがすぐにテキストを入力できる状態にすることで、ユーザーエクスペリエンス（UX）を向上させることができる

## 関数コンポーネント配下のメソッドを参照する
```
import { forwardRef, useImperativeHandle, useRef } from "react";

const MyTextBox = forwardRef(({ label }, ref) => {
  const input = useRef(null);

  useImperativeHandle(
    ref,
    () => {
      return {
        focus() {
          input.current.focus();
        },
      };
    },
    []
  );

  return (
    <label>
      {label}：
      <input type="text" size="15" ref={input} />
    </label>
  );
});

export default MyTextBox;
```
1. forwardRef:  
ReactのRefを子コンポーネントに転送（フォワード）するためのヘルパーです。通常、RefはReactのコンポーネントで直接使用することはできませんが、forwardRefを使うとその制限を越えられます。

2. useRef:  
ReactのRefを作成します。この例では、input という名前でRefを作成して、input タグに接続しています。

3. useImperativeHandle:  
親コンポーネントが子コンポーネントのRef経由で実行できる関数を定義するフックです。この例では、focus() というメソッドを公開して、このメソッドが呼ばれたら内部の input にフォーカスを当てます。

### 基本の処理の流れ
1. MyTextBox コンポーネントがレンダリングされます。
2. 内部で input Refが作成され、input タグに接続されます。
3. useImperativeHandle が ref として渡されたオブジェクトに focus メソッドを追加します。
4. 親コンポーネントがこの ref を使って focus() メソッドを呼び出すことができ、それによってこの input 要素にフォーカスが当たります。
  
このコードの利点は、親コンポーネントから簡単に内部のinput要素にフォーカスを当てられるようにすることです。これにより、より柔軟なコンポーネントの制御が可能になります。

## コールバック関数をref属性に引き渡す-コールバックRef
```
import { useEffect, useRef, useState } from 'react';

export default function HookCallbackRef() {
  const [show, setShow] = useState(false);
  const handleClick = () => setShow(!show);
  const address = useRef(null);
  useEffect(() => {
    if (address.current) {
      address.current.focus();
    }
  }, [show]);

  const callbackRef = elem => elem?.focus();

  return (
  <>
  <div>
      <label htmlFor="name">名前：</label>
      <input id="name" type="text" />
  </div>
  <div>
      <label htmlFor="email">メールアドレス：</label>
      <input id="email" type="text" />
      <button onClick={handleClick}>拡張表示</button>
  </div>
  {show &&
    <div>
      <label htmlFor="address">住所：</label>
      <input id="address" type="text" ref={callbackRef} />
    </div>
  }
  </>
  );
}
```
1. useState:
show というステート変数を作成しています。これがtrueのときに住所（address）の入力フィールドが表示されます。

2. useRef:
address という名前でRefを作成しています。
このRefはuseEffect内で使われますが、コード中では直接使用されていません。

3. useEffect:
このフックはshowが変更されたときに実行されます。
address.currentが存在する場合（住所の入力フィールドが表示されている場合）、そのフィールドにフォーカスを当てます。

4. callbackRef:
コールバックRefと呼ばれる手法です。この関数は、ref属性として指定されると、対象のDOM要素（ここではinput要素）がマウントされた際に呼び出されます。この関数内でelem?.focus();を実行して、即座にその要素にフォーカスを当てています。

#### 動作の流れ
1. 初期状態ではshowがfalseなので、住所の入力フィールドは非表示です。

2. 「拡張表示」ボタンをクリックすると、handleClick関数が実行されてshowがtrueになり、住所の入力フィールドが表示されます。

3. 住所の入力フィールドが表示されたときに、useEffectとcallbackRefが実行され、その結果として住所の入力フィールドにフォーカスが当たります。
  
このコードの利点は、条件付きで表示される入力フィールドに自動でフォーカスを当てられる点です。これにより、ユーザーがすぐに入力できるようになり、UX（ユーザーエクスペリエンス）が向上します。

#### useEffectとcallbackRefの役割の違い
useEffect: 
- これは**showが変更されたとき**に実行されます。
- もしshowがtrueになり、住所の入力フィールドが表示された場合、このフックが実行されてフォーカスが当たります。
- ただし、この例ではaddress refが住所の入力フィールドには紐づけられていないので、実際にはこの部分は動作しないでしょう。
  
callbackRef: 
- これは**住所の入力フィールドがDOMに追加された瞬間に実行**されます。
- 具体的には、showがtrueになったとき、条件付きでレンダリングされる住所の入力フィールドがDOMに追加され、このコールバックが呼び出されます。
  
要するに、`useEffectはステート変更`に応じて、`callbackRefはDOM要素のマウント（追加）やアンマウント（削除）`に応じて動作する

この例では、useEffectは実際には動作しないので、住所の入力フィールドにフォーカスを当てる役割はcallbackRefが担っている
