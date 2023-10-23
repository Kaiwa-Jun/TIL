## useStateフックの問題点
### useStateの問題点
1. 疎結合の問題:  
useStateを使用すると、ステート更新処理が**コンポーネント内のいろいろな場所に散在する**可能性がある。その結果、コードの可読性とメンテナンス性が低下する可能性がある

2. ロジックの再利用:  
ステートの更新ロジックが**コンポーネント内に閉じ込められている**ため、同じロジックを別のコンポーネントで使いたいと思った場合に**再利用が難しくなる**

3. テストの困難性:  
複数のuseStateが組み合わさった複雑なステートロジックは、テストする際に手間がかかることがあります。

4. コンポーネントの肥大化:  
複数のuseStateとそれに伴う多くのステート更新処理が一つのコンポーネント内に集まると、そのコンポーネントが非常に大きく、複雑になるリスクがあります。

5. 非同期問題:  
useStateによるステート更新は非同期に行われるため、依存関係や競合状態が発生する可能性があります。

### useReducerによる解決
1. 集中管理:  
useReducerを使用することで、**ｖステート更新ロジックを一箇所に集中させる**ことができます。

2. 可読性とメンテナンス性の向上:  
**ステートの更新方法が一箇所にまとまる**ため、コードが読みやすく、メンテナンスも容易になります。

3. ロジックの再利用:  
Reducer関数は純粋な関数であり、**外部のコンポーネントやカスタムフックで簡単に再利用できる**

4. テストの容易性:  
Reducer関数は独立してテストできるため、ユニットテストが書きやすくなります。

5. 高度なステート管理:  
useReducerはより複雑なステート管理が可能であり、非同期処理や中間状態も扱いやすくなります。

## useReducerに関わるキーワード
### Reducer
- State更新に利用する関数
- `useReducer`フックでは`State`を初期化したり更新する際に`Reducer`を用意する
- `State`の更新はReducer経由で行う
- `dispach`関数に対してActionを渡すことで`Reducer`を呼び出せる

### Action
- `Action`は`Reducer`へ渡す指示書
  - 更新の種別（typeプロパティ）
  - 更新に関わるパラメータ（任意のプロパティ）
 
## useReducerフックの基本
```
import { useReducer } from 'react';

export default function HookReducer({ init }) {

  //①StateとReducerの準備
  const [state, dispatch] = useReducer(

    //Reducer関数
    (state, action) => {
      switch (action.type) {

        //②
        case 'update':
          return { count: state.count + 1 };

        //③
        default:
          return state;
      }
    },
    {
      count: init
    }
  );

  const handleClick = () => {

    //④
    dispatch({ type: 'update' });
  };

  return (
    <>
      <button onClick={handleClick}>カウント</button>
      <p>{state.count}回、クリックされました。</p>
    </>
  );
}
```
①StateとReducerの準備:  
- useReducerフックは、第一引数にReducer関数、第二引数に初期状態（init）を取ります。
- 戻り値として`state（現在の状態）とdispatch（アクションを送信する関数）`が返されます。
```
const [state, dispatch] = useReducer(reducer, { count: init });
```

②updateケース:  
- Reducer関数内でswitch文を用いて、アクションのtypeに応じた処理を行っています。
- updateというtypeが来た場合は、カウントを1増やします。

```
case 'update':
  return { count: state.count + 1 };
```

③デフォルトケース:  
- 一致するtypeがない場合には、現在のstateをそのまま返します。これは一般的な慣習です。
```
default:
  return state;
```

④dispatchの呼び出し:  
- handleClick関数内で、dispatch関数を用いてtype: 'update'というアクションを送信します。
- このdispatchが呼び出されると、Reducerが再度実行されてstateが更新されます。
```
const handleClick = () => {
  dispatch({ type: 'update' });
};
```

## Reducerを複数のAction型に対応する
```
import { useReducer } from 'react';

export default function HookReducerUp({ init }) {
  const [state, dispatch] = useReducer(
    (state, action) => {

      //①
      switch (action.type) {
        case 'update':

          //②
          return { count: state.count + action.step };
        case 'reset' :

          //③
          return { count: action.init };
        default:
          return state;
      }
    },
    {
        count: init
    }
  );

  //④
  const handleUp = () => dispatch({ type: 'update', step: 1 });
  const handleDown = () => dispatch({ type: 'update', step: -1 });
  const handleReset = () => dispatch({ type: 'reset', init: 0 });

  return (
  <>
    <button onClick={handleUp}>カウントアップ</button>
    <button onClick={handleDown}>カウントダウン</button>
    <button onClick={handleReset}>リセット</button>
    <p>{state.count}回、クリックされました。</p>
  </>
  );
}
```
①switch文:  
- switch文を用いてアクションのtypeに応じた処理を行います。

②updateケース:  
- typeがupdateの場合にcountを更新しますが、action.stepを使用して増減の値を動的に指定できます。
```
return { count: state.count + action.step };
```

③resetケース:  
- typeがresetの場合にcountをaction.initにリセットします。
```
return { count: action.init };
```

④アクションのディスパッチ:  
- 各ボタンがクリックされた場合に、dispatch関数を使って適切なtypeとパラメーター（step, init）を送ります。

```
const handleUp = () => dispatch({ type: 'update', step: 1 });
const handleDown = () => dispatch({ type: 'update', step: -1 });
const handleReset = () => dispatch({ type: 'reset', init: 0 });
```
  
**useReducerのメリット**  
状態の更新ロジックが一箇所にまとまる:  
- useReducerを使用することで、状態の更新ロジックが一箇所（Reducer内）に集まります。
- このことで、初心者のWebエンジニアでもコードの流れが把握しやすく、保守が容易になります。

動的な操作が容易:  
- この例では、action.stepやaction.initなど、動的な値を使って状態を更新しています。
- これもuseReducerが有用なケースです
