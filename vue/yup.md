# yupによるフォームバリデーション実装手順

本ドキュメントは、Vue 3（Composition API） + yup を使った  
**型安全なバリデーション**を組み込むための標準手順をまとめたものです。

---

## 1. 事前準備

### 1.1 必要なパッケージインストール

```bash
npm install yup @vee-validate/yup vee-validate
```
- yup：バリデーションスキーマ定義ライブラリ

- vee-validate：Vue用のフォームバリデーションライブラリ

- @vee-validate/yup：vee-validateとyupを連携させるブリッジ

## 2. バリデーションスキーマ作成
型ごとにスキーマを作成します。


### 2.1 実装例（User作成フォーム）
```ts
// src/validations/userSchema.ts
import * as yup from 'yup';

export const userCreateSchema = yup.object({
  name: yup.string().required('名前は必須です').max(50, '最大50文字以内です'),
  email: yup.string().required('メールアドレスは必須です').email('正しいメールアドレス形式にしてください'),
});

```


## 3. 作成したバリデーションスキーマの使用

### 3.1 useFormにバリデーションスキーマを設定する

事前に作成したスキーマを `useForm` の `validationSchema` オプションに渡す。

#### 例

```vue
<script setup lang="ts">
import { useForm } from 'vee-validate';
import { toTypedSchema } from '@vee-validate/yup';
import { userCreateSchema } from '@/validations/userSchema'; // スキーマをインポート

const { handleSubmit, errors, values: fields } = useForm({
  validationSchema: toTypedSchema(userCreateSchema),
});

// 送信処理
const onSubmit = (values: any) => {
  console.log('登録データ', values);
};
</script>
```

- `validationSchema` に `toTypedSchema(スキーマ)` を渡すことで `yup `連携

- `errors` と `values`（または別名 `fields`）が自動で管理される

- `handleSubmit` にバリデーション付きの `submit` ハンドラをセットできる

## 3.2 フォームとの連携
`values` を `v-model` で双方向バインディングし、エラーは `errors` で表示

```ts
<template>
  <form @submit.prevent="handleSubmit(onSubmit)">
    <div>
      <label>名前</label>
      <input v-model="fields.name" type="text" />
      <span v-if="errors.name">{{ errors.name }}</span>
    </div>

    <div>
      <label>メールアドレス</label>
      <input v-model="fields.email" type="email" />
      <span v-if="errors.email">{{ errors.email }}</span>
    </div>

    <button type="submit">登録</button>
  </form>
</template>
```

## 3.3 useField併用パターン

フィールド単位で細かく制御したいときは、
useForm（スキーマ適用）＋ useField（個別フィールド操作）を併用します。

```ts
const { handleSubmit } = useForm({
  validationSchema: toTypedSchema(formSchema),
});

// フィールド単位にバインディング
const { value: text, errorMessage: textError } = useField<string>('text');
```

# 4 参考リンク
- [yup](https://github.com/jquense/yup )

- [VeeValidate](https://vee-validate.logaretm.com/v4/guide/components/validation#using-a-schema)
- 日本語解説記事：[composition APIでyupとvee-validateを使ったバリデーションをする](https://zenn.dev/cryptobox/articles/0c442917e2b47b)