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

## ドロワーメニューを実装する
```
import { useState } from 'react';
import { Home, Mail, Info, AccountTree } from '@mui/icons-material';
import { Box, Button, Drawer, List, ListItem, ListItemButton,
  ListItemText, ListItemIcon } from '@mui/material';

const menu = [
  { title: 'ホーム', href: 'home.html', icon: Home },
  { title: '問い合わせ', href: 'contact.html', icon: Mail },
  { title: '会社概要', href: 'company.html', icon: Info  },
  { title: 'サイトマップ', href: 'sitemap.html', icon: AccountTree },
];

export default function MaterialDrawer() {

  // ②
  const [show, setShow] = useState(false);
  const handleDraw = () => setShow(!show);

  return (
    <>
    <Button onClick={handleDraw}>ドローワー</Button>

    // ①
    <Drawer anchor="left" open={show}>
      <Box sx={{ height: '100vh' }} onClick={handleDraw}>

      // ③
      <List>
      {menu.map(obj => {
        const Icon = obj.icon;
        return (
        <ListItem key={obj.title}>
          <ListItemButton href={obj.href}>
            <ListItemIcon><Icon /></ListItemIcon>
            <ListItemText primary={obj.title} />
          </ListItemButton>
        </ListItem>
        );
      })}
      </List>
      </Box>
    </Drawer>
    </>
  );
}
```
①<Drawer>要素
- 下記のように属性を設定できる
| 属性 | 概要 | 規定値 |
|:---|:---:|---:|
|anchor |ドロワーが現れる方向（bottom,left,right,top） |left |
|elevation |立体の度合い |16 |
|hideBackdrop |trueでドロワー表示時にメイン画面を灰色にしない |false |
|open |5 |ドロワーを表示するか |
|onClose |ドロワーを閉じた時に実行すべき処理 |- |
    
②ドロワーの開閉
- 属性値として渡したState値（show）をクリックで反転させている

③<List>要素
- List,ListItem,ListButton,ListItemIcom,ListItemAvatar,ListItemTextなど

### ページ内の配置を調整するレイアウト機能を活用する-グリッド
```
import { Button } from '@mui/material';
import Grid from '@mui/material/Unstable_Grid2';

export default function MaterialGrid() {
  return (
  <Grid container spacing={2}>
    <Grid xs={6}>
       <Button variant="contained" fullWidth>1</Button>
    </Grid>
    <Grid xs={2}>
       <Button variant="contained" fullWidth>2</Button>
    </Grid>
    <Grid xs={3}>
       <Button variant="contained" fullWidth>3</Button>
    </Grid>
    <Grid xs={12}>
       <Button variant="contained" fullWidth>4</Button>
    </Grid>
  </Grid>
  );
}
```
- 1行12列が基本
- 例では、xsが6、2、３、１２なので、1行目は6:2:3で分割
- 2行目に12分が表示される
