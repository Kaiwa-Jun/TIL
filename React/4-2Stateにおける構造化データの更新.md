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
## 配列の更新
