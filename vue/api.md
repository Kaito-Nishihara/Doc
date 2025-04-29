# APIクライアント自動生成ガイド（hey-api/openapi-ts版）

---

## 1. 概要

本ドキュメントは、Vue 3（Composition API）プロジェクトにおいて、  
**OpenAPI（Swagger）仕様からAPIクライアントコードを自動生成する**  
標準手順をまとめたものです。

- `@hey-api/openapi-ts` を使用して、API型定義とSDKクライアントを自動生成
- Axiosベースの通信ライブラリを生成
- 型安全なフロントエンド開発を実現

---

## 2. 事前準備

### 2.1 必要なパッケージインストール

```bash
npm install @hey-api/openapi-ts
```

## 3. 設定ファイル作成
### 3.1 openapi-ts.config.ts を作成
プロジェクトルート（例: / または /config/配下）に以下の内容で作成します。
```
// openapi-ts.config.ts
import { defineConfig } from '@hey-api/openapi-ts';

export default defineConfig({
  input: 'http://localhost:5183/swagger/v1/swagger.json', // API仕様書URL
  output: {
    format: 'prettier',  // 出力後にPrettierで整形
    lint: 'eslint',      // 出力後にESLintチェック
    path: './src/api',   // 出力先フォルダ
  },
  plugins: [
    '@hey-api/client-axios',   // Axiosクライアント生成
    '@hey-api/schemas',        // スキーマ型定義生成
    {
      dates: true,
      name: '@hey-api/transformers', // 日付型変換をサポート
    },
    {
      enums: 'javascript',
      name: '@hey-api/typescript',   // EnumをJavaScript互換形式で出力
    },
    {
      name: '@hey-api/sdk',     // 使いやすいSDKラッパー生成
      transformer: true,
    },
  ],
});

```

## 4. APIクライアント生成コマンド
### 4.1 コマンド実行

```bash
npx openapi-ts
```
もしくは、package.jsonにスクリプト登録

```json
{
  "scripts": {
    "openapi-ts": "openapi-ts"
  }
}
```

## 5. 生成物の構成

```plantext
/src/api/
  ├── client.gen.ts     // Axiosクライアント本体
  ├── types.gen.ts      // OpenAPIから生成された型定義
  ├── schemas.gen.ts    // APIスキーマ定義
  ├── sdk.gen.ts        // API呼び出し用SDK関数群
```

`types.gen.ts`：型安全なリクエスト・レスポンス型

`sdk.gen.ts`：実際に使うためのメソッド（例: `getUserList()`, `createUser()`）

## 6. 使用例

```ts
import { sdk } from '@/api/sdk.gen';

const users = await sdk.getUserGetUsers();
console.log(users);
```

## 7. 参考リンク
- [@hey-api/openapi-ts GitHub](https://github.com/hey-api/openapi-ts)

- [Swagger（OpenAPI Specification）公式](https://swagger.io/specification/)