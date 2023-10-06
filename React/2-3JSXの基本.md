## JSXとは
`JaveScriptのコード内にHTMLさながらのタブを埋め込む`しくみ

複数の要素を列挙できない
```
// NGパターン
root.render(
  <p>こんにちは！</p>
  <p>初めまして！、React！</p>
)
```
```
// OKパターン
root.render(
  <>
    <p>こんにちは！</p>
    <p>初めまして！、React！</p>
  </>
)

root.render(
  <div>
    <p>こんにちは！</p>
    <p>初めまして！、React！</p>
  </div>
)

root.render(
  <React.Fragment>
    <p>こんにちは！</p>
    <p>初めまして！、React！</p>
  </React.Fragment>
)
```

## JSXにJavaScriptを埋め込む
```
const name = '鈴木';
root.render(
  <p>こんにちは！{name}です！</p>
)
```
