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
npm install -D typescript @types/node ts-node
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

