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
```bash
cd ../client
npm install lodash @types/lodash
```
```bash
npm install react-query axios
```
```bash
npm install react-toastify react-hook-form nookies
```
### コンポーネント作成(Atomic Design)
Atomic Design について
詳しくは、こちらを見てください。
https://www.codegrid.net/articles/2017-atomic-design-1/

各コンポーネントのディレクトリを作成します。
```bash
mkdir -p components/atoms
mkdir -p components/molecules
mkdir -p components/organisms
mkdir -p components/templates
mkdir -p components/pages
```

#### Atomic Design 役割
| グループ名 | 組合せ可 | 関わる人 | 担保するもの         | 特徴                                                                                                            |
| :--------- | :------- | :------- | :------------------- | :-------------------------------------------------------------------------------------------------------------- |
| atoms      | x        | dev      | 基本デザイン         | ・UIパーツの最小構成部品<br>・他のコンポーネントには依存しない<br>・配置パターンもatomsに含まれる               |
| molecules  | o        | dev      | 操作性               | ・atomsを組み合わせて作る部品<br>・atomsの原始的行動に意味を付加する<br>・eg.文字を入れる+クリックする=検索する |
| organisms  | o        | dev      | 機能、価値、サービス | ・atoms,molecules,organismsを組み合わせて作る部品<br>・独立して機能し、他のページでも同じ意図で使える           |
| templates  | o        | dev+biz  | レイアウト           | ・atoms,molecules,organismsを配置する<br>・プレイスホルダーだけのスケルトンの状態                               |
| pages      | o        | dev+biz  | 流し込むデータ       | ・templatesにデータを流し込みページを動かす                                                                     |


#### 作成一覧
サンプルでは、CSSユーティリティの `tailwindcss` を用いて画面デザインを作成します。
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
| Pages     | IndexPage       | トップページ                       |
| Pages     | LoginPage       | ログインページ                     |
| Pages     | PasswordDialog  | パスワード変更ダイアログ           |

#### Atoms （原子）
##### Button コンポーネント作成
`Material-UI`などのUIコンポーネントのAPI仕様を参考にコンポーネントの仕様を決めていきます。ボタンカラー、サイズ、追加class、disabledなどを指定できるようにしています。
```bash
vi components/atoms/button.tst
```
```tsx

```

#### Molecules （分子）

#### Organisms （有機体）

#### Templates （テンプレート）

#### Page （ページ）