## コンテキストの基本
```
// HookContext.js
import { createContext } from 'react';
import { HookContextChild } from './HookContextChild';

//①コンテキストの初期化
export const MyAppContext = createContext();

//③コンテキストに渡すためのオブジェクトを準備
const config = {
  title: 'React入門',
  lang: 'ja-JP',
};

export default function HookContext() {
  return (

    //②配下の要素に対してコンテキストを適用
    <MyAppContext.Provider value={config}>
      <div id="c_main">
        <HookContextChild />
      </div>
    </MyAppContext.Provider>
  );
}
```

```
// HookContextChild.js
import { useContext } from 'react';
import { MyAppContext } from './HookContext';

export function HookContextChild() {
  return (
    <div id="c_child">
      <HookContextChildGrand />
    </div>
  );
}

export function HookContextChildGrand() {

  //④
  const { title, lang } = useContext(MyAppContext);
  return (
    <div id="c_child_grand">

      //⑤
      {title}（{lang}）
    </div>
  );
}

```
①コンテキストの初期化:  
- コンテキストは値を共有すべき最上位のコンポーネントで準備する
- `createContext()`を使って新しいコンテキストを作成します。
  - コンテキストに値を引き渡す`Context.Provider`コンポーネント
  - コンテキストの変更を受け取る`Context.Consumer`コンポーネント
```
export const MyAppContext = createContext();
```

②配下の要素に対してコンテキストを適用:  
- `MyAppContext.Provider`を使って、このコンポーネントの子要素にデータを提供します。
- value属性でコンテキストに値を渡している（今回は③で準備したconfigオブジェクト）

```
<MyAppContext.Provider value={config}>
```

③コンテキストに渡すためのオブジェクトを準備:  
- configオブジェクトを作って、titleとlangの情報を持たせています。

```
const config = {
  title: 'React入門',
  lang: 'ja-JP',
};
```

④コンテキストからデータを取得:  
- 孫コンポーネントでuseContext()を使用して、親から渡されたデータを取得します。
- createContext関数で作成されたコンテキストを引数に渡す（今回はMyAppContext）

```
const { title, lang } = useContext(MyAppContext);
```

⑤データの表示:  
- 取得したtitleとlangを使って画面に表示します。
```
{title}（{lang}）
```
  
- この方法の利点は、HookContextとHookContextChildGrandが直接の親子関係でなくても、HookContextChildGrandはHookContextからデータを受け取ることができる点です。
- これによって、コンポーネントの再利用性が高まり、データの「流れ」が明確になります。

### PropsとuseContextの違い
#### Props

- 直接の親子関係: Propsは直接の親子関係にあるコンポーネント間でのデータの受け渡しに適しています。
- シンプルなデータフロー: データの流れが一方向であり、狭いスコープに限定されるため、管理が容易です。
- 再利用性: 同じデータを使い回すよりは、必要なコンポーネントごとにPropsで渡す方が単純です。
- Prop Drilling問題: 複数の階層を超えるデータの受け渡しには不便で、管理が煩雑になる可能性があります。

#### useContext

- 複数の階層: 直接の親子関係がない、または複数の階層を跨いでデータを受け渡す必要がある場合に適しています。
- 共通データ: 多くのコンポーネントで同じデータを使用する場合に便利です。
- データフローの明確化: コンテキストを通じてデータが流れるため、どのコンポーネントがどのデータに依存しているかが明確になります。
- 過度な使用のリスク: 使いすぎるとコードが複雑になり、データフローが不明瞭になる可能性があります。
