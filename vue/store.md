# Piniaによる状態管理（Store）実装手順

本ドキュメントは、Vue 3（Composition API） + Pinia を使った  
**型安全な状態管理（Store）** を組み込むための標準手順をまとめたものです。

---

## 1. 事前準備

### 1.1 必要なパッケージインストール

```bash
npm install pinia
```

## 2. Piniaセットアップ

### 2.1 main.tsで登録する
PiniaをVueアプリにインストールして使用できるようにします。

```ts
// main.ts
import { createApp } from 'vue';
import { createPinia } from 'pinia';
import App from './App.vue';

const app = createApp(App);
app.use(createPinia());
app.mount('#app');
```

## 3. Store作成
### 3.1 基本構成
- Piniaでは、defineStore関数でストアを作成します。
```ts
// src/stores/userStore.ts
import { defineStore } from 'pinia';

export const useUserStore = defineStore('user', {
  state: () => ({
    name: '',
    email: '',
  }),
  getters: {
    displayName: (state) => `${state.name} <${state.email}>`,
  },
  actions: {
    setName(newName: string) {
      this.name = newName;
    },
    setEmail(newEmail: string) {
      this.email = newEmail;
    },
  },
});
```

- `state`: データの初期値を定義する

- `getters`: 計算プロパティ（computed）を定義する

- `actions`: 状態を変更するためのメソッドを定義する

### 3.2 Composition API版
- より柔軟に管理したい場合は、Setupスタイルも使用可能

```ts
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export const useUserStore = defineStore('user', () => {
  const name = ref('');
  const email = ref('');

  const displayName = computed(() => `${name.value} <${email.value}>`);

  function setName(newName: string) {
    name.value = newName;
  }

  function setEmail(newEmail: string) {
    email.value = newEmail;
  }

  return { name, email, displayName, setName, setEmail };
});
```
- `ref`, `computed`, `watch`などVue標準の`Composition API`がそのまま使える
### 3.3 使用上の注意点：Storeの状態はリロードで失われる
- Piniaの state は、ブラウザリロードやタブを閉じると初期化されます。

- ストアの状態はメモリ上にのみ存在しているため、ページをリロードすると状態はすべてリセットされます。

永続化が必要な場合は、**ローカルストレージ保存（永続化）** を別途設定する必要があります。



## 4. コンポーネントでの使用

### 4.1 インポートして使用する

```vue
<script setup lang="ts">
import { useUserStore } from '@/stores/userStore';

const userStore = useUserStore();
</script>

<template>
  <div>
    <p>ユーザー名: {{ userStore.name }}</p>
    <p>表示名: {{ userStore.displayName }}</p>

    <input v-model="userStore.name" placeholder="名前を入力" />
    <input v-model="userStore.email" placeholder="メールアドレスを入力" />

    <button @click="userStore.setName('テストユーザー')">名前をセット</button>
  </div>
</template>
```

## 5. Storeの永続化（オプション）

Piniaの状態をローカルストレージに保存するには、<br>
pinia-plugin-persistedstate を使うことができます。

### 5.2 セットアップ

```ts
// main.ts
import { createPinia } from 'pinia';
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate';

const pinia = createPinia();
pinia.use(piniaPluginPersistedstate);

app.use(pinia);
```

### 5.2 ストア側で指定

```ts
export const useUserStore = defineStore('user', {
  state: () => ({
    name: '',
    email: '',
  }),
  persist: true,
});
```

## 6. 参考リンク

- [Pinia公式ドキュメント](https://pinia.vuejs.org/)
- [Pinia persistedstate Plugin](https://prazdevs.github.io/pinia-plugin-persistedstate/)
- [Zenn Pinia入門](https://zenn.dev/comm_vue_nuxt/articles/6ff9a9024cdae4)
- [状態管理ライブラリPiniaについて](https://zenn.dev/caffe_latte_623/articles/9e06b9c3e56fb3)