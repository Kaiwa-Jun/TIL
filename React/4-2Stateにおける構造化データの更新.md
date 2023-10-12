## スプレッド構文の意味
### 入れ子の例
```
// StateNest.js
import { useState } from 'react';

export default function StateNest() {

  // ①入れ子の配列をStateとして宣言
  const [form, setForm] = useState({
    name: '山田太郎',
    address: {
      prefecture: '広島県',
      city: '榛原町'
    }
  });

  // ②1段目の要素を更新するための関数
  const handleForm = e => {
    setForm({
      ...form,
      [e.target.name]: e.target.value
    });
  };

  // ③2段目の要素を更新するための関数
  const handleFormNest = e => {
    setForm({
      ...form,
      address: {
        ...form.address,
        [e.target.name]: e.target.value
      }
    });
  };

  const show = () => {
    console.log(`${form.name}（${form.address.prefecture}・${form.address.city}）`);
  };

  return (
    <form>
      <div>
        <label htmlFor="name">名前：</label>
        <input id="name" name="name" type="text"
          onChange={handleForm} value={form.name} />
      </div>
      <div>
        <label htmlFor="prefecture">住所（都道府県）：</label>
        <input id="prefecture" name="prefecture" type="text"
          onChange={handleFormNest} value={form.address.prefecture} />
      </div>
      <div>
        <label htmlFor="city">住所（市町村）：</label>
        <input id="city" name="city" type="text"
          onChange={handleFormNest} value={form.address.city} />
      </div>
      <div>
        <button type="button" onClick={show}>
          送信</button>
      </div>
    </form>
  );
}
```
①入れ子配列をStateとして宣言
  
②handleForm関数
- 名前の入力フィールドが変更されたときに呼び出される
- この関数内で、スプレッド構文と計算されたプロパティ名を使用して、新しいformオブジェクトを作成し、setForm関数を使って状態を更新する
  
③handleFormNest関数
- 住所の入力フィールドが変更されたときに呼び出される
- この関数内で、スプレッド構文を2回使用している
- 1回目はformオブジェクトのプロパティを新しいオブジェクトにコピーするため
- 2回目はform.addressオブジェクトのプロパティを新しいaddressオブジェクトにコピーするため


## Immerライブラリによる改善
```
// StateNestImmer.js
import { useImmer } from 'use-immer';

export default function StateNestImmer() {
  // ①
  const [form, setForm] = useImmer({
    name: '山田太郎',
    address: {
      prefecture: '広島県',
      city: '榛原町'
    }
  });

  // ②
  const handleForm = e => {
    setForm(form => {
      form[e.target.name] = e.target.value;
    });
  };

  // ③
  const handleFormNest = e => {
    setForm(form => {
      form.address[e.target.name] = e.target.value;
    });
  };

  const show = () => {
    console.log(`${form.name}（${form.address.prefecture}・${form.address.city}）`);
  };

  return (
    <form>
      <div>
        <label htmlFor="name">名前：</label>
        <input id="name" name="name" type="text"
          onChange={handleForm} value={form.name} />
      </div>
      <div>
        <label htmlFor="prefecture">住所（都道府県）：</label>
        <input id="prefecture" name="prefecture" type="text"
          onChange={handleFormNest} value={form.address.prefecture} />
      </div>
      <div>
        <label htmlFor="city">住所（市町村）：</label>
        <input id="city" name="city" type="text"
          onChange={handleFormNest} value={form.address.city} />
      </div>
      <div>
        <button type="button" onClick={show}>
          送信</button>
      </div>
    </form>
  );
}
```
①：フォームとして扱う値をStateとして宣言
  
②③：状態の更新
- useImmerはImmerライブラリの能力を利用して状態の更新を行う
- setForm関数は、新しい状態を直接変更することができる
- handleFormとhandleFormNest関数は、setForm関数を使用して状態を更新する
- これらの関数は、現在の状態のドラフトを受け取り、そのドラフトを直接変更することができる

  
**スプレッド構文を使用した状態の更新（先ほどのコード）:** 
- 元の状態をコピーして新しいオブジェクトを作成し、特定のプロパティを更新します。  
- ネストされたオブジェクトの場合、各レベルでスプレッド構文を使用してオブジェクトをコピーする必要があります。  
```
const handleFormNest = e => {
  setForm({
    ...form,
    address: {
      ...form.address,
      [e.target.name]: e.target.value
    }
  });
};
```
  
**Immerを使用した状態の更新（下記コード）:**
- Immerは状態のドラフトバージョンを提供し、このドラフトを直接変更できます。
- ネストされたオブジェクトでも、ドラフトを直接変更するだけで良いので、スプレッド構文は必要ありません。
```
const handleFormNest = e => {
  setForm(form => {
    form.address[e.target.name] = e.target.value;
  });
};
```
**比較結果:**
- Immerを使用すると、コードはより簡潔になり、ネストされたプロパティの更新が直接的で簡単になります。
- スプレッド構文を使用すると、特にネストされたオブジェクトを扱う場合にコードが冗長になりがちで、エラーが発生しやすくなります。
- Immerの主な利点は、状態の更新をシンプルかつ直感的に行えることで、コードの可読性と保守性が向上します。

### ハンドラーを共通化する
このコードでは、handleNest関数を作成して、異なるフォーム要素の変更を一つの関数で処理している
これにより、handleFormとhandleFormNestのような個別のハンドラー関数を作成する必要がなくなり、ハンドラーの共通化が実現される
```
import { useImmer } from 'use-immer';

export default function StateNestImmer2() {
  const [form, setForm] = useImmer({
    name: '山田太郎',
    address: {
      prefecture: '広島県',
      city: '榛原町'
    }
  });

  // ②
  const handleNest = e => {
    const ns = e.target.name.split('.');

    // ③
    setForm(form => {
      if (ns.length === 1) {
        form[ns[0]] = e.target.value;
      } else {
        form[ns[0]][ns[1]] = e.target.value;
      }
    });
  };

  const show = () => {
    console.log(`${form.name}（${form.address.prefecture}・${form.address.city}）`);
  };

  return (
    <form>
      <div>
        <label htmlFor="name">名前：</label>

        // ①
        <input id="name" name="name" type="text"
          onChange={handleNest} value={form.name} />
      </div>
      <div>
        <label htmlFor="prefecture">住所（都道府県）：</label>

        // ①
        <input id="prefecture" name="address.prefecture" type="text"
          onChange={handleNest} value={form.address.prefecture} />
      </div>
      <div>
        <label htmlFor="city">住所（市町村）：</label>

        // ①
        <input id="city" name="address.city" type="text"
          onChange={handleNest} value={form.address.city} />
      </div>
      <div>
        <button type="button" onClick={show}>
          送信</button>
      </div>
    </form>
  );
}
```
①name属性の値を確認:  
- 各入力要素のname属性には、状態オブジェクトの対応するプロパティへのパスが指定されています。
- 例えば、name="address.prefecture"の場合、これは状態オブジェクトのaddressオブジェクトのprefectureプロパティを指します。
  
②name属性の値を分割:
- handleNest関数内でe.target.name.split('.')を使用して、name属性の値を.で分割しています。
- これにより、nsという変数には、プロパティのパスの各部分が配列として格納されます。
  
③状態の更新:
- setForm関数を呼び出し、状態のドラフトバージョンを更新しています。
- ns.length === 1の条件をチェックして、プロパティのパスが1レベルか2レベルかを判断しています。
- 1レベルの場合（例: name）、form[ns[0]] = e.target.value;で状態を更新しています。
- 2レベルの場合（例: address.prefecture）、form[ns[0]][ns[1]] = e.target.value;で状態を更新しています。


## 配列の更新
- 配列型のStateは直接更新することはできないため、下表のルールでメソッドを選択する
- 既存のStateオブジェクトや配列を直接変更せず、新しいオブジェクトや配列を作成してStateを更新するため
  
| 操作 | ○利用すべき | ×避けるべき |
|:---|:---|:---|
|追加 |concat、[...list] |push、unshift |
|更新 |map |splice、list[i]=〜 |
|削除 |filter、slice |pop、shift、splice |
|ソート |あらかじめ配列を複製 |sort、reverse |


### Todoアプリの例
```
import { useState } from 'react';
import './StateTodo.css';

let maxId = 0;
export default function StateTodo() {
  const [desc, setDesc] = useState(true);
  const [title, setTitle] = useState('');
  const [todo, setTodo] = useState([]);

  const handleChangeTitle = e => {
    setTitle(e.target.value);
  };

  const handleClick = () => {

    // ①新規のTodoの追加
    setTodo([
      // ②
      ...todo,
      // ③
      {
        id: ++maxId,
        title,
        created: new Date(),
        isDone: false
      }
    ]);
  };

  // ⑤済みボタンから呼び出される関数
  const handleDone = e => {

    // ⑥todo配列をｍapで走査して、id値が等しいものを検索
    setTodo(todo.map(item => {
      if (item.id === Number(e.target.dataset.id)) {
        return {
          ...item,
          isDone: true
        };

      // ⑦
      } else {
        return item;
      }
    }));
  };

  // ⑩
  const handleRemove = e => {
    setTodo(todo.filter(item =>
      item.id !== Number(e.target.dataset.id)
    ));
  };


  const handleSort = e => {
    // ❶既存のTodoリストを複製の上でソート
    const sorted = [...todo];
    sorted.sort((m, n) => {
      if (desc) {
        return n.created.getTime() - m.created.getTime();
      } else {
        return m.created.getTime() - n.created.getTime();
      }
    });

    // desc値を変転
    setDesc(d => !d);

    // ❷ソート済みのリストを再セット
    setTodo(sorted);
  };

  return (
    <div>
      <label>
        やること：
        <input type="text" name="title"
          value={title} onChange={handleChangeTitle} />
      </label>
      <button type="button"
        onClick={handleClick}>追加</button>
      <button type="button"
        onClick={handleSort}>
         ソート（{desc ? '↑' : '↓'}）</button>
      <hr />
      {/* <ul>
        {todo.map(item => (
          <li key={item.id}>{item.title}</li>
          ))}
      </ul> */}
      <ul>
        {todo.map(item => (
          <li key={item.id}

            // ⑧
            className={item.isDone ? 'done' : ''}>
            {item.title}

            // ④済ボタン
            <button type="button"
              onClick={handleDone} data-id={item.id}>済
            </button>

            // ⑨
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

### 配列への追加 - Todoの新規登録
①Todoの新規登録
  
②push/unshiftなどの破壊的メソッドが使えないので、「...」演算子（スプレッド構文）で元の配列を複製
  
③新規要素を追加

### 配列への更新 - Todoの済チェック
④Todo項目を特定するためにdata-id属性に各項目のidプロパティを割り当てる

⑤済みボタンから呼び出される関数

⑥todo配列をｍapで走査して、id値が等しいものを検索
- map関数は新しい配列を作成し、各要素に対して特定の処理を実行する
- この場合、現在のid値（item.id）とdata-id属性（e.target.dataset.id）が一致する要素のisDoneプロパティをtrueに変更
  - item.id:
    - todo配列の各要素（item）は、ユニークな識別子としてidプロパティを持っている
    - これは、配列内の各要素を一意に識別するために使用される
  - e.target.dataset.id:
    - eはイベントオブジェクトで、イベントハンドラーhandleDoneに渡される
    - このイベントオブジェクトは、イベントに関連する情報を提供する
    - e.targetは、イベントが発生したHTML要素（この場合はボタン）を参照する
    - datasetは、HTML要素に設定されたdata-*属性の集合を参照する
    - この場合、data-id属性が設定されており、dataset.idでその値を取得できる
    - Number()関数は、dataset.idの値を数値に変換している
    - dataset.idは文字列として取得されるため、数値に変換してitem.id（数値）と比較できるようにしている
  - item.id === Number(e.target.dataset.id):
    - この比較は、現在処理中のitemのidが、クリックされたボタンのdata-id属性の値と一致するかどうかを確認している
    - 一致する場合、このitemは対象のTodo項目であり、そのisDoneプロパティをtrueに設定される

- その他の要素はそのまま保持(⑦) 
- この処理は、元のtodo配列を直接変更せずに、新しいtodo配列を作成している


⑦その他の要素は更新の対象外のため、そのまま元の要素を返す

⑧idDoneプロパティがtrueである項目にdoneスタイルクラスを付与して、取り消し線を表示させる

### 配列への削除 - Todo項目の破棄
⑨削除ボタンにdata-id属性を追加して、Todo項目を特定できるようにする
⑩配列要素の削除の関数
- filterメソッドを利用
  - 条件に合致した要素を抽出
  - 今回はid値（item.id）とdata-id属性（e.target.dataset.id）が合致しない要素だけを残すことで削除機能を実現している

### 配列の並べ替え - Todo項目の昇順／降順
❶既存のTodoリストを複製の上でソート
- State値は直接更新できないので、「...todo」で複製
❷ソート済みのリストを再セット
