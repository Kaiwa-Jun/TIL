## Recoilとは
- Recoil
  - React用の状態管理ライブラリ。
  - 大規模なアプリケーションで特に有用。

- Atom（アトム）
  - 状態管理の最小単位。
  - 一つの状態（State）を保持。
  - 複数のコンポーネントで共有可能。
  - 状態が変更されると、依存するコンポーネントが自動更新。

- State（状態）
  - アプリケーションの現在のデータ。
  - AtomとSelectorで管理。
  - 状態が変わると、購読しているコンポーネントが再レンダリング。

- Selector（セレクタ）
  - 導出状態を計算する関数。
  - 一つまたは複数のAtom/Selectorから新しい状態を導出。
  - 読み取り専用だが、セッター関数で状態変更可能。
  - 依存するAtomが更新されると自動で再計算。

## Recoilの基本
### Atomを準備する
```
import { atom, selector } from 'recoil';

export const counterAtom = atom({
  key: 'counterAtom',
  default: 0
});
```
atom関数に対してストア情報（オブジェクト）を渡す
- key:ストアを一意に特定するキー(複数あるAtomを一意に特定するため)
- default:ストアの規定値
- Atomの粒度はできるだけ細かく
- Atomは複数設定する
- 依存するAtomが小さいほどAtomの変更に伴う再描画の範囲も限定されるため

### Atomを参照する
```
import { useRecoilState } from 'recoil';
import { counterAtom } from '../store/atom';

export default function RecoilCounter() {

  //①
  const [counter, setCounter] = useRecoilState(counterAtom);

  const handleClick = () => {

    //②
    setCounter(c => c + 1);
  };

  return (
    <>
      <button onClick={handleClick}>カウント</button>
      <p>{counter}回、クリックされました。</p>
    </>
  );
}
```
1. useRecoilState フックの使用
- useRecoilState は Recoil の提供する React フックです。
- Atomを読み書きするためにこのフックを使用します。
- ここで const [counter, setCounter] = useRecoilState(counterAtom);（①）のように、Atomから状態（State）を取得（counter）および更新（setCounter）することができます。

2. 状態の更新
- setCounter（②）は Atom の状態を変更するための関数です。
- この関数を使って、現在のカウンターの値（c）を取得し、その値に1を加算しています。

3. 状態の表示
- `<p>{counter}回、クリックされました。</p>`で、取得したcounterの値を画面に表示しています。

4. 便利性と効率
- Atomを使うと、状態の読み取りや変更が容易になります。
- 依存するAtomが小さいほど、そのAtomの変更に伴う再描画も最小限に抑えられます。

### コンポーネントを呼び出す
```
root.render(
  <RecoilRoot>
    <RecoilCounter />
  </RecoilRoot>
);
```
- Recoilを利用する最上位のコンポーネントを`<RecoilRoot>`で括る
- これで配下のコンポーネントでRecoilが使えるようになる

## TodoリストをRecoilアプリに対応する
### Atom/Selectorを準備する
```
import { atom, selector } from 'recoil';

export const counterAtom = atom({
  key: 'counterAtom',
  default: 0
});

//①Todoリストの定義
export const todoAtom = atom({
  key: 'todosAtom',
  default: [
    {
      id: 1,
      title: 'WINGS会議の資料作成',
      isDone: false,
    },
    {
      id: 2,
      title: '報酬料の振込',
      isDone: true,
    },
    {
      id: 3,
      title: 'A書籍のサポートページ作成',
      isDone: true,
    },
    {
      id: 4,
      title: '読者質問への回答',
      isDone: false,
    },
    {
      id: 5,
      title: 'B出版社と打ち合わせ',
      isDone: false,
    },
  ],
});

//②
export const todoLastIdSelector = selector({
  key: 'todoLastIdSelector',
  get: ({ get }) => {
    const todo = get(todoAtom);
    return todo.at(-1)?.id ?? 0;
  },
});
```
①Todoリストの定義
- id値
- Todoの本体
- 実行済みか

②selectorの定義
- Atomの値を加工／演算したものを取得するゲッター
- selector関数に構成要素を渡す
  - key:ストアを一意に特定するキー
  - get:セレクターの本体

### コンポーネントをRecoil対応に修正する
```
import { useRecoilState, useRecoilValue } from 'recoil';
import { useState } from 'react';
import { todoAtom, todoLastIdSelector } from '../store/atom';
import '../chap04/StateTodo.css';

export default function RecoilTodo() {
  const [title, setTitle] = useState('');
  const [todo, setTodo] = useRecoilState(todoAtom);
  const maxId = useRecoilValue(todoLastIdSelector);

  const handleChangeTitle = e => setTitle(e.target.value);
  const handleAdd = () => {
    setTodo([
      ...todo,
      {
        id: maxId + 1,
        title,
        isDone: false
      }
    ]);
  };
  const handleDone = e => {
    setTodo(todo.map(item => {
      if (item.id === Number(e.target.dataset.id)) {
        return {
          ...item,
          isDone: true
        };
      } else {
        return item;
      }
    }));
  };
  const handleRemove = e => {
    setTodo(todo.filter(item =>
      item.id !== Number(e.target.dataset.id)
    ));
  };

  return (
    <div>
      <label>
        やること：
        <input type="text" name="todo"
          value={title} onChange={handleChangeTitle} />
      </label>
      <button type="button"
        onClick={handleAdd}>追加</button>
      <hr />
      <ul>
        {todo.map(item => (
          <li key={item.id}
            className={item.isDone ? 'done' : ''}>
            {item.title}
            <button type="button"
              onClick={handleDone} data-id={item.id}>済
            </button>
            <button type="button"
              onClick={handleRemove} data-id={item.id}>削除
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```
1. 状態管理の変更
- 通常のReactの useState を用いていた部分（title）はそのままです。
- Todoリストの状態は useRecoilState(todoAtom) で取得・設定します。
- 最後のID値は useRecoilValue(todoLastIdSelector) で取得します。

2. Todo追加機能
- handleAdd 関数で、新しいTodoを追加します。
- 最後のID値（maxId）を用いて新しいIDを生成しています。

3. Todo完了・削除機能
- handleDone と handleRemove 関数で、Todoの完了状態を更新またはTodoを削除します。
- これも setTodo を用いてRecoilの状態を更新しています。

## Todoリストを改良する
前述のコードはTodoリストをまとめて単一のAtomで管理しているため、特定の項目を追加／更新すると、配列をまとめて置き換える必要がある
- コードが煩雑になる
- 処理負荷がかかる

### Atom/Selectorを準備する
```
import { atom, atomFamily, selector } from 'recoil';

//②
export const idsAtom = atom({
  key: 'idsAtom',
  default: []
});

//①
export const todoAtom = atomFamily({
  key: 'todoAtom',
  default: null
});

//③
export const todoListSelector = selector({
  key: 'todoListSelector',

  //④
  get: ({ get }) => {
    const ids = get(idsAtom);
    return ids.map(id => get(todoAtom(id)));
  },

  //⑤
  set: ({ set, reset }, { type, id, newItem }) => {
    switch (type) {
      case 'add' :
       set(todoAtom(newItem.id), newItem);
       set(idsAtom, ids => [...ids, newItem.id]);
       break;
      case 'done' :
        set(todoAtom(id), todo => ({ ...todo, isDone: true }));
        break;
      case 'remove' :
        reset(todoAtom(id));
        set(idsAtom, ids => ids.filter(e => e !== id));
        break;
      default :
        throw new Error('Type is invalid.');
    }
  }
});
```
**①Atom群を管理するのはatomFamily関数**
- key/defaultを設定
- 個々のAtomにアクセスするにはtodoAtom(12)のようにAtom群からAtomを特定するためのキーを指定する

**②Todoリストを管理するためのid群を定義**
- Todo項目のidだけをまとめた配列を別のAtomとして管理する

**③todoListSelectorを用意**
- id群（idAtom）、Todo項目（todoAtom）をまとめて管理するselector
- ゲッター(④)とセッター(⑤)を用意

**④ゲッター**
- idsAtomで走査
- 個々のTodo項目（todoAtom）を取得

**⑤セッター**  
セッターはselector内で定義されており、setおよびreset関数と共に、操作の種類(type)、TodoのID(id)、新しいTodo項目(newItem)を引数として受け取ります。
- set: AtomまたはSelectorの状態を更新するための関数。
- reset: Atomの状態をデフォルト値にリセットするための関数。
  
セッター内の処理は次の通りです：
 
| 呼び出し | 概要 |
|:---|:---:|
|set({ type:'add', newItem:item }) |Todoリストに新規項目itemを追加 |
|set({ type:'done', id: id }) |Todo項目idを済としてマーク |
|set({ type:'remove', id: id }) |Todo項目idを削除|

### コンポーネントを修正
```
import { useRecoilState } from 'recoil';
import { useState } from 'react';
import { idsAtom, todoListSelector } from '../store/atomUp';
import '../chap04/StateTodo.css';

export default function RecoilTodoUp() {
  const [title, setTitle] = useState('');
  const [todo, setTodo] = useRecoilState(todoListSelector);
  const [ids, setIds] = useRecoilState(idsAtom);

  const handleChangeTitle = e => {setTitle(e.target.value)};

  const handleAdd = () => {

    //④
    const newId = Math.max(...(ids.length ? ids : [0])) + 1;

    //①
    setTodo({
      type: 'add',
      newItem: {
        id: newId,
        title,
        isDone: false
      }
    });
  };

  //②
  const handleDone = e => {
    setTodo({
      type: 'done',
      id: Number(e.target.dataset.id)
    });
  };

  //③
  const handleRemove = e => {
    setTodo({
      type: 'remove',
      id: Number(e.target.dataset.id)
    });
  };

  return (
    <div>
      <label>
        やること：
        <input type="text" name="todo"
          value={title} onChange={handleChangeTitle} />
      </label>
      <button type="button"
        onClick={handleAdd}>追加</button>
      <hr />
      <ul>
        {todo.map(item => (
          <li key={item.id}
            className={item.isDone ? 'done' : ''}>
            {item.title}
            <button type="button"
              onClick={handleDone} data-id={item.id}>済
            </button>
            <button type="button"
              onClick={handleRemove} data-id={item.id}>削除
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```
① handleAdd関数（Todo追加）
- 最大のid値を取得して、新しいid（newId）を生成します（④）。
- 新しいTodo項目（id, title, isDone）を作成し、setTodoでtodoListSelectorのセッターを呼び出してTodoを追加します。

② handleDone関数（Todo完了）
- ボタンがクリックされたTodoのidを取得します。
- そのidを使用して、setTodoでtodoListSelectorのセッターを呼び出し、該当するTodo項目を完了状態（isDone: true）に更新します。
  
③ handleRemove関数（Todo削除）
- 削除ボタンがクリックされたTodoのidを取得します。
- そのidを使用して、setTodoでtodoListSelectorのセッターを呼び出し、該当するTodo項目を削除します。
  
④ newIdの生成
- 現在のids配列から最大のid値を探し、1加算して新しいid（newId）を生成します。
