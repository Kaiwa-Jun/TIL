## React Hook Formの基本
```
import { useForm } from 'react-hook-form';
import './FormBasic.css';

export default function FormBasic() {
  const defaultValues = {
    name: '名無権兵衛',
    email: 'admin@example.com',
    gender: 'male',
    memo: ''
  };

  // ①フォームを初期化
  const { register, handleSubmit,
    formState: { errors, isDirty, isValid, isSubmitting } } = useForm({
    defaultValues
  });

// ⑤サブミット時の処理
  const onsubmit = data => console.log(data);
  const onerror = err => console.log(err);

  return (

  // ④
  <form onSubmit={handleSubmit(onsubmit, onerror)} noValidate>
    <div>
      <label htmlFor="name">名前：</label><br/>

      // ②
      <input id="name" type="text"
        {...register('name', {
          required: '名前は必須入力です。',
          maxLength: {
            value: 20,
            message: '名前は20文字以内にしてください。'
          }
        })}
      />

      // ③
      <div>{errors.name?.message}</div>
    </div>
    <div>
      <label htmlFor="gender">性別：</label><br/>
      <label>
      <input type="radio" value="male"
        {...register('gender', {
          required: '性別は必須です。',
        })} />男性
      </label>
      <label>
      <input type="radio" value="female"
        {...register('gender', {
          required: '性別は必須です。',
        })} />女性
      </label>
      <div>{errors.gender?.message}</div>
    </div>
    <div>
      <label htmlFor="email">メールアドレス：</label><br/>
      <input id="email" type="email"
        {...register('email', {
          required: 'メールアドレスは必須入力です。',
          pattern: {
            value: /([a-z\d+\-.]+)@([a-z\d-]+(?:\.[a-z]+)*)/i,
            message: 'メールアドレスの形式が不正です。'
          }
        })} />
      <div>{errors.email?.message}</div>
    </div>
    <div>
      <label htmlFor="memo">備考：</label><br/>
      <textarea id="memo"
        {...register('memo', {
          required: '備考は必須入力です。',
          minLength: {
            value: 10,
            message: '備考は10文字以上にしてください。'
          },
          validate: {
            ng: (value, formValues) => {
              const ngs = ['暴力', '死', 'グロ'];
              for (const ng of ngs) {
                if (value.includes(ng)) {
                  return '備考にNGワードが含まれています。';
                }
              }
              return  true;
            }
          },
        })} />
      <div>{errors.memo?.message}</div>
    </div>
    <div>
      <button type="submit">送信</button>
      {/* <button type="submit"
        disabled={!isDirty || !isValid}>送信</button> */}
      {/* <button type="submit"
        disabled={!isDirty || !isValid || isSubmitting}>送信</button>
        {isSubmitting && <div>...送信中...</div>} */}
    </div>
  </form>
  );
}
```
①フォームを初期化
- useForm フックを利用してフォームの状態管理を初期化する。
- defaultValues をパラメータとして渡すことで、各フォームフィールドの初期値を設定する。
  
②register メソッドを利用する
- 各フォームコントロール（input, radio button等）に register メソッドを適用することで、react-hook-form の管理下に置く。
- register メソッドにはバリデーションルールをオブジェクトとして渡すことができ、これにより入力値のチェックを行う。
  
③エラーメッセージの表示
- errors オブジェクトから各フィールドのエラーメッセージを取得し、条件付きレンダリングを利用してエラーメッセージを表示する。
  
④フォームのサブミット処理
- handleSubmit メソッドを利用して、フォームのサブミット時の処理を定義する。
- onSubmit と onError という2つの関数を handleSubmit メソッドに渡すことで、サブミット成功時とエラー時の処理を分けて定義する
  
⑤サブミット時の処理
- onSubmit 関数では、サブミット成功時にフォームデータをコンソールに出力する。
- onError 関数では、サブミットエラー時にエラーオブジェクトをコンソールに出力する。

## 独自の検証ルールを実装する
```
<label htmlFor="memo">備考：</label><br />
<textarea id="memo"
  {...register("memo", {
    required: "備考は必須入力です。",
            minLength: {
              value: 10,
              message: "備考は10文字以上にしてください。",
            },
  validate: {
    ng: (value, formValues) => {

    // 不適切ワードを準備
    const ngs = ["暴力", "死", "グロ"];

    // 入力文字列に不適切ワードが含まれるか判定
    for (const ng of ngs) {
      if (value.includes(ng)) {
        return "備考にNGワードが含まれています。";
      }
    }
    return true;
  },
},
})}
/>
```
validateオプションによって独自の検証ルールを適用することができる

1. ラベルとテキストエリアの設定:

- <label htmlFor="memo">備考：</label>: ラベル要素を作成し、htmlForプロパティを利用して、このラベルがmemoというidを持つテキストエリアに関連付けられていることを指定しています。
- <textarea id="memo" {...register("memo", {...})} />: テキストエリア要素を作成し、idプロパティを設定しています。そして、react-hook-formのregisterメソッドを利用してバリデーションルールを適用しています。

2. バリデーションルールの設定:

- required: "備考は必須入力です。": requiredルールは、このフィールドが必須であることを指定し、エラーメッセージを提供します。
- minLength: { value: 10, message: "備考は10文字以上にしてください。" }: minLengthルールは、入力値が特定の最小文字数を持つことを要求し、エラーメッセージを提供します。

3. カスタムバリデーションルールの設定:

- validate: {...}: validateプロパティを利用して、カスタムバリデーションルールを設定します。
- ng: (value, formValues) => {...}: ngという名前のカスタムバリデーションルールを定義します。このルールは、テキストエリアの値（value）を受け取り、特定の単語（暴力, 死, グロ）が含まれているかどうかをチェックします。
- if (value.includes(ng)) { return "備考にNGワードが含まれています。"; }: includesメソッドを利用して、値にNGワードが含まれているかどうかを確認し、含まれていればエラーメッセージを返します。
- return true;: NGワードが含まれていない場合、バリデーションは成功と見なされ、trueを返します。

## フォームの状態に応じて表示を制御する
```
  const {
    register,
    handleSubmit,

    // ①
    formState: { errors, isDirty, isValid, isSubmitting },
  } = useForm({
    defaultValues,
  });


<button type="submit"
        disabled={!isDirty || !isValid}>送信</button>
```
①isDirtyがfalse（フォームが変更されない）、isValidがfalse（検証に失敗）の場合は、disabledが有効（ボタンが無効になる）

## 検証ライブラリを連携する
## Yupで独自の検証ルールを実装する
## Yupで入力値を変換する
## Yupのエラーメッセージを日本語化する
