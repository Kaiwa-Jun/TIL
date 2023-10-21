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
