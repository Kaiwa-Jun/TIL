# フォントと画像の最適化
## なぜフォントを最適化するのか？
- カスタムフォントはウェブデザインに重要だが、パフォーマンスに影響を与える可能性がある。
- Googleの「Cumulative Layout Shift」は、フォントの読み込みによるレイアウト変更を評価する指標。
- Next.jsはnext/fontモジュールでフォントを自動最適化し、パフォーマンスに影響を与えない。
- カスタムGoogleフォントを追加することで、この最適化の効果を確認できる。

## `<Image>`コンポーネント
<Image>コンポーネントは、HTMLの<img>タグを拡張したもので、以下のような自動画像最適化機能を備えている：

- 画像の読み込み時に自動的にレイアウトがずれないようにする。
- ビューポートが小さいデバイスに大きな画像を送信しないように、画像のサイズを変更します。
- デフォルトで画像を遅延ロード（ビューポートに入ると画像がロードされる）。
- ブラウザがサポートしている場合、WebP やAVIF のような最新のフォーマットで画像を提供します。

```
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import { lusitana } from '@/app/ui/fonts';
import Image from 'next/image';
 
export default function Page() {
  return (
    // ...
    <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
      {/* Add Hero Images Here */}
      <Image
        src="/hero-desktop.png"
        width={1000}
        height={760}
        className="hidden md:block"
        alt="Screenshots of the dashboard project showing desktop and mobile versions"
      />
    </div>
    //...
  );
}
```
