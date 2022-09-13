# Next.js による管理ページ作成
管理ページのサンプルを作成にあたり、技術情報をまとめてみました。

### 機能一覧
以下の機能等をサンプル実装していきます。
| 機能                 | 説明                                 |
| :------------------- | :----------------------------------- |
| 認証                 | ユーザー認証、認証画面表示制御       |
| オートログイン       |                                      |
| 状態管理             |                                      |
| 一覧表示・ページング |                                      |
| フォーム             | フォーム入力のバリデーション         |
| キャッシュ制御       |                                      |
| トースト表示         |                                      |
| ダイアログ表示       |                                      |
| コンポーネント管理   | Atomic Design レイアウト制御等       |
| ローディング表示     | ページ遷移時・非同期通信時に表示処理 |

### フロントエンド実装
以下のライブラリ等を利用して、画面を作成します。
| 名前            | 説明                               |
| :-------------- | :--------------------------------- |
| Next.js         | React フロントエンドフレームワーク |
| TypeScript      |                                    |
| React Hook Form | フォームライブラリ                 |
| yup             | バリデーション                     |
| React Query     | 非同期ライブラリ                   |
| tailwindcss     | CSSユーティリティ                  |
| React Tosastfy  | トースト表示                       |
| nookies         | Cookie用ヘルパー                   |

### バックエンド実装
以下のライブラリを利用して、バックエンド環境を実装します。
| 名前    | 説明 |
| :------ | :--- |
| Express |      |
| JWT     |      |

## プロジェクトを作成
```bash
mkdir nextjs2admin && cd nextjs2admin
```
### フロントエンドプロジェクトを作成
```bash
mkdir client && cd client
npx create-next-app@latest ./ --ts
cd ../
```
### バックエンドプロジェクトを作成
```bash
mkdir server && cd server
npm init -y
npm install -D typescript @types/node ts-node nodemon
npx tsc --init
npm install express
npm install -D @types/express
```
## バックエンドの制作
バックエンドサーバーとして`express` にてAPIサーバーを構築していきます。
### 作成するAPI
| エンドポイント        | メソッド | 名前             | 説明                           |
| :-------------------- | :------- | :--------------- | :----------------------------- |
| /api/auth             | POST     | ログイン         | ユーザー認証を行う             |
| /api/auth/signout     | POST     | ログアウト       | ユーザーログアウト             |
| /api/auth/check       | POST     | トークンチェック | JWT のチェック                 |
| /api/password/change  | POST     | パスワード変更   |                                |
| /api/auth/code/verify | POST     | 認証コード検証   | パスワード変更要求の確認に利用 |
| /api/products         | GET      | 商品情報一覧     |                                |
| /api/products/post    | POST     | 商品情報登録     |                                |
| /api/products/put     | PUT      | 商品情報更新     |                                |
| /api/products/delete  | DELETE   | 商品情報削除     |                                |

### http サーバーを作成する
```bash
mkdir routes
```
```bash
vi routes/index.ts
```
```typescript
import { Request, Response, Router } from "express"

const router: Router = Router()

router.get('/', (req: Request, res: Response) => {
	res.send('Index Page')
});

export default router
```
```bash
vi server.ts
```
```ts
import Express from "express"
import http from 'http'
import indexRouter from './routes/index'

const app = Express()
const httpServer = http.createServer(app)
const PORT = process.env.PORT || 5000

app.use(Express.json())
app.get('/', indexRouter)

httpServer.listen(PORT, () => {
	console.log(`Server listening on port ${PORT}`)
})
```
開発用httpサーバー起動コマンドを登録
`package.json`に`"dev": "npx nodemon server.ts"`を追記する。
```bash
vi package.json
```
```json
{
  "name": "server",
  "version": "1.0.0",
  "description": "",
  "main": "server.ts",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "npx nodemon server.ts",
	"start": "ts-node server.ts"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@types/express": "^4.17.13",
    "@types/node": "^18.7.17",
    "nodemon": "^2.0.19",
    "ts-node": "^10.9.1",
    "typescript": "^4.8.3"
  },
  "dependencies": {
    "express": "^4.18.1"
  }
}
```
```bash
npm run dev
```
無事に起動すると以下の様にコンソールに表示されます。
```bash
Server listening on port 5000
```

## フロントエンドの制作

### コンポーネント作成(Atomic Design)
Atomic Design について
詳しくは、こちらを見てください。
https://www.codegrid.net/articles/2017-atomic-design-1/

サンプルでは、CSSユーティリティの `tailwindcss` を用いて画面デザインを作成します。

#### 作成一覧
| レベル    | 名前            | 説明                               |
| :-------- | :-------------- | :--------------------------------- |
| Atoms     | Button          |                                    |
| Atoms     | Link            |                                    |
| Atoms     | Typography      |                                    |
| Atoms     | FormLabel       | 入力フォームらべる                 |
| Molecules | Pager           | 検索一覧のページネーション         |
| Molecules | TabHeader       | 検索テーブルのヘッダ               |
| Organisms | Dashboard       | トップページのダッシュボードエリア |
| Organisms | SearchableTable | 再検索可能なテーブル               |
| Templates | Confirm         | 確認ダイアログテンプレート         |
| Templates | DashboardLayout | 管理画面レイアウト                 |
| Page      | IndexPage       | トップページ                       |
| Page      | LoginPage       | ログインページ                     |
| Page      | PasswordDialog  | パスワード変更ダイアログ           |