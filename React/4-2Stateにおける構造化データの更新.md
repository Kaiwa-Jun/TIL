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



