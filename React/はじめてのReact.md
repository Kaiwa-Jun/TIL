1. プロジェクトの作成</br>
```
npm create-react-app my-react
```

2. アプリの実行</br>
```
cd my-react
npm start
```

- 補足：Npm Scripts
npm startコマンドの正体は、package.jsonで定義されたショートカットコマンドで、`Npm Scripts`とも言う。
```
{
  "name": "my-react",
  中略
  "scripts": {
    "start": "react-scripts start",
    中略
  },
  中略
}
```
