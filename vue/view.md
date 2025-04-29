# View画面作成手順ガイド

本ドキュメントは、Vue 3 + Composition API を使ったSPA開発において、  
画面コンポーネントの作成手順を体系的にまとめたものです。

## 1. 作成の基本手順

### 1.1 ファイル作成

- `/src/views/` 配下に画面ごとの `.vue` ファイルを作成する
- ファイル名はパスカルケース推奨（例：`UserList.vue`, `UserEdit.vue`）


### 1.2 テンプレート基本構成

```vue
<template>
  <div class="p-4">
    <!-- 画面レイアウト・フォーム・一覧などをここに -->
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue';
</script>

<style >
/* 必要に応じてスタイルを追加 */
</style>
```


## 2. Composition API基本パターン
Composition APIでは、状態管理・ライフサイクル処理・イベントハンドラを  
すべて `script setup` 内でシンプルに管理します。

### 2.1 状態管理（State）

- フォームの単一フィールド → `ref`
- フォームの複数フィールド（オブジェクト）→ `reactive`


#### 例：

```ts
const userName = ref(''); // 単一フィールド
const form = reactive({
  email: '',
  password: ''
}); // 複数フィールド
```

## 2.2 ライフサイクル処理
- 
- 詳細は [こちら]() を参照


## 2.3  API呼び出し
