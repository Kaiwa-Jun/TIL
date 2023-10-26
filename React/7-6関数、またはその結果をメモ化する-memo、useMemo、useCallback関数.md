## メモ化のためのサンプル

**メモ化とは？**
値をブラウザのメモリに保存すること
値を簡単に取得できるようになる

```
import { useMemo, useCallback, useState } from 'react';
import { MyButton, MyCounter } from './HookMemoChild';

const sleep = delay => {
  const start = Date.now();
  while (Date.now() - start < delay);
};

export default function HookMemo() {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);
  const increment = () => setCount1(c => c + 1);
  const decrement = () => setCount2(c => c - 1);


  const heavyProcess = () => {
    sleep(1000);
    return count1 + 100;
  };

  return (
    <>
      <div>
      <MyButton id="btn1" handleClick={increment}>カウントアップ</MyButton>
      <MyCounter id="c1" value={count1} />／
      {heavyProcess()}
      </div>
      <div>
      <MyButton id="btn2" handleClick={decrement}>カウントダウン</MyButton>
      <MyCounter id="c2" value={count2} />
      </div>
    </>
  );
}
```
```
import { memo } from 'react';

export const MyButton = ({ id, handleClick, children }) => {
  console.log(`MyButton is called: ${id}`);
  return (
    <button onClick={handleClick}>{children}</button>
  );
};

export const MyCounter = ({ id, value }) => {
  console.log(`MyCounter is called: ${id}`);
  return (
    <p>現在値：{value}</p>
  );
};

```
### 問題点
両ボタンをクリックしてもStateが変化して、HookMemoがコンポーネントが再描画される
HookMemoがコンポーネントが再描画される度にheavyPrecess関数が実行され、反応が遅れる
直接の影響がないcount2も遅れる

## 関数の結果をメモ化する-useMemo関数
```
 const heavyProcess = useMemo(() => {
   sleep(1000);
   return count1 + 100;
 }, [count1]);

 ```
State値（count1）が変化した場合だけ、heabyProcess関数が実行される  
count2に影響がないので、カウントダウンは即座に結果が反映される  
**値だけをメモ化できる**

### 問題点
カウントアップ、ダウンともにコンソールにMyButton×２、MyCounter×２の4つのログが出る  
State（count1,count2）の更新によってHookMemoコンポーネントが再描画され、配下のコンポーネントも再描画されるため    
無駄な際描画となる  

## コンポーネントの際描画を抑制する-memo関数
```
export const MyButton = memo(({ id, handleClick, children }) => {
  console.log(`MyButton is called: ${id}`);
  return <button onClick={handleClick}>{children}</button>;
});

export const MyCounter = memo(({ id, value }) => {
  console.log(`MyCounter is called: ${id}`);
  return <p>現在値：{value}</p>;
});

```
MyButton×２、MyCounter×１の３つのログになる


## 関数定義そのものをキャッシュする-useCallback関数
```
const increment = useCallback(() => setCount1(c => c + 1), []);
const decrement = useCallback(() => setCount2(c => c - 1), []);
```
Mycounterコンポーネントのみ再描画される  
**関数だけをメモ化できる**
