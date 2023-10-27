## Prismaの準備
### Prismaのインストール
```
npm install prisma --save-dev
```

### 自動生成されたファイルを確認する
```
/prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}
```

```
/.env
DATABASE_URL="file:./dev.db"
```

### データモデルを定義する
PrismaのようなO/Rマッパーでは、DBの各列とオブジェクトの紐付けを担う
```
/prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "sqlite"
  url      = env("DATABASE_URL")
}

model reviews {
  id        String      @id
  title     String
  author    String
  price     Int
  publisher String
  published String
  image     String
  read      DateTime   @default(now())
  memo      String
}
```

### データベースを作成する
```
npx prisma db push
```

### データベースの内容を確認する
```
npx prisma studio
```

### クライアントオブジェクトを準備する
```
/ src/lib/prisma.js
import { PrismaClient } from '@prisma/client';

// global.prisma上にPrismaクライアントが存在すれば再利用
const prisma = global.prisma ??
  new PrismaClient({ log: ['query'] });
// 非プロダクション環境ではglobal.prismaにオブジェクトを格納
if (process.env.NODE_ENV !== 'production') global.prisma = prisma;

export default prisma;
```
