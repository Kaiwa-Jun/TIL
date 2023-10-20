## MUIの主なコンポーネント
## MUIの基本
```
import { css } from '@emotion/react';
import { Button } from '@mui/material';

export default function MaterialBasic() {
  const font = css`
      text-transform: none;
  `;
  return (
    <>
    <Button variant="text">Text</Button>
    <Button variant="contained">Contained</Button>
    <Button variant="outlined">Outlined</Button>

    <Button variant="text" color="secondary">Text</Button>
    <Button variant="contained" color="secondary">Contained</Button>
    <Button variant="outlined" color="secondary">Outlined</Button>

    {/* <Button variant="text" css={font}>Text</Button>
    <Button variant="contained" css={font}>Contained</Button>
    <Button variant="outlined" css={font}>Outlined</Button> */}
    </>
  );
}
```
インポート:
- css関数はEmotion（CSS-in-JSライブラリ）からインポートされています。
- ButtonはMUIの@mui/materialからインポートされています。
    
変数font:
- css関数を使って、テキストの変形を無効にするCSSを定義しています（text-transform: none;）。
    
ボタンの種類:
- variantプロパティでボタンの見た目を変更しています。
- それぞれ"text", "contained", "outlined"という3つの種類があります。

    
ボタンの色:
- colorプロパティでボタンの色を指定しています。
- "secondary"と指定しているボタンは、セカンダリーカラー（通常はテーマで定義されています）が適用されます。
