## ストーリーの確認
StoryBookの表示のためには下記ファイルが必要
- コンポーネント本体（.js/.cssファイル）
- コンポーネントの表示方法（.storied.jsファイル）

### ByButtonコンポーネントの準備
```
import PropTypes from 'prop-types';
import '../stories/button.css';

export default function MyButton ({
    primary = false,
    backgroundColor = null,
    size = 'medium',
    label = 'Button',
    // handleClick,
    ...props
  }) {
  const mode = primary ?
    'storybook-button--primary' : 'storybook-button--secondary';
  return (
    <button
      type="button"
      className={['storybook-button', `storybook-button--${size}`, mode].join(' ')}
      style={backgroundColor && { backgroundColor }}
      // onClick={handleClick}
      {...props}
    >
      {label}
    </button>
  );
};
```
MyButtonコンポーネントで利用できるProps
| Props | 概要 |
|:---|:---:|
|primary |Primaryカラーで描画するか |
|backgroundColor |ボタンの背景色 |
|size |ボタンの大きさ |
|label |ボタンのキャプション |
|onClick |クリック時の呼び出されるハンドラー |

### MyButtonコンポーネントのストーリー
コンポーネント本体が準備できたため、対応するストーリー（MyButton.stories.js）を準備する
```
import { action } from '@storybook/addon-actions';
import { userEvent, within } from '@storybook/testing-library';
import { expect } from '@storybook/jest';
import MyButton from './MyButton';

// ①
export default {
  title: 'MyApp/MyButton',
  component: MyButton,
};

// ②Index,White,Yellowストーリーを追加
export const Index = {
  render: () => <MyButton primary size="medium" label="ボタン"
     onClick={() => console.log('Hello, Storybook!!')}/>
};

export const White = {
   render: () => <MyButton size="small" label="ボタン"
     backgroundColor="#fff" />
};

export const Yellow = {
  args: {
    ...White.args,
    backgroundColor: 'lightyellow'
  }
};
```
①基本情報（メタデータ）
- 次表のようなプロパティを含んだオブジェクトをexport defaultできる
- 最低限title、componentプロパティを指定しておけばいい
- titleは/で階層を持たせられる
| プロパティ | 概要 |
|:---|:---:|
|title |表示タイトル |
|component |対象となるコンポーネント |
|args |Propsの規定値 |
|argTypes |Propsの型情報 |
|decorators |ストーリーの表示を修飾するためのデコレーター |
|parameter |Storybookの見た目／挙動を設定するパラメータ情報 |

②Index,White,Yellowストーリーを追加
- renderオプションで描画コードを指定するのがシンプル
- renderオプションはJSX式を返す関数であること
