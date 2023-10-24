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

## 例：コンテキストを利用してテーマを切り替えを実装する
使用するコード
| ファイル名 | 概要 |
|:---|:---:|
|ThemaContext.js |コンテキストを定義 |
|MyThemaProvider |テーマ／コンテキストの設定 |
|HookThemaButton.js |テーマ切り替えのボタンの準備 |

### コンテキストの準備（ThemaContext.js）
```
import { createContext } from 'react';

export default createContext({
  mode: 'light',
  toggleMode: () => {}
});
```
- ここでcreateContextを使って、新しいコンテキストを作成しています。
- 初期値としてmodeをlightに設定し、toggleModeは空の関数で初期化しています。
- このコンテキストは後でプロバイダー（MyThemaProvider.js）で上書きされることを想定しています。

### ルートコンポーネントの準備（MyThemaProvider.js）
```
import { useState } from 'react';
import { createTheme, ThemeProvider } from '@mui/material/styles';
import { amber, grey } from '@mui/material/colors';
import { CssBaseline } from '@mui/material';
import ThemeContext from './ThemeContext';

export default function MyThemeProvider({ children }) {
  const [mode, setMode] = useState('light');
  const themeConfig = {
    mode,
    toggleMode: () => {
      setMode(prev =>
        prev === 'light' ? 'dark' : 'light'
      );
    }
  };
  const theme = createTheme({
    mode,
    palette: {
      mode,
      ...(mode === 'light'
      ? {
          primary: amber,
        }
      : {
        primary: {
          main: grey[500],
          contrastText: '#fff'
        },
        background: {
          default: grey[900],
          paper: grey[900],
        },
      }),
    },

  });
  return (
    <ThemeContext.Provider value={themeConfig}>
      <ThemeProvider theme={theme}>
        <CssBaseline />
        {children}
      </ThemeProvider>
    </ThemeContext.Provider>
  );
}
```
- useStateを使って現在のテーマモード（mode）を管理しています。
- createThemeはMUIライブラリの関数で、テーマの設定を行います。
- ThemeProviderとCssBaselineはMUIライブラリのコンポーネントで、全体のスタイリングを制御します。
- themeConfigは、コンテキストを通して他のコンポーネントにmodeとtoggleModeを提供するためのオブジェクトです。
```
  const themeConfig = {
    mode,
    toggleMode: () => {
      setMode(prev =>
        prev === 'light' ? 'dark' : 'light'
      );
    }
  };
```
- toggleMode関数は、現在のmodeをlightからdarkへ、またはその逆へと切り替えます。

### コンテキストの取得(HookThemaButton.js)
```
import { useContext } from 'react';
import { Button } from '@mui/material';
import ThemeContext from './ThemeContext';

export default function HookThemeButton() {
  const { mode, toggleMode } = useContext(ThemeContext);
  return (
    <Button variant="contained" onClick={toggleMode}>
      Mode {mode}
    </Button>
  );
}
```
- useContextフックを使ってThemeContextからmodeとtoggleModeを取得します。
- ボタンには、mode（現在のモード）が表示され、クリックするとtoggleMode関数が呼び出されてモードが切り替わります。
