# Composition API

本ドキュメントは、Vue 3 Composition API における  
**主要なライフサイクルフックの使い方・注意点**をまとめたもの。

詳しくは [Composition API: ライフサイクルフック](https://ja.vuejs.org/api/composition-api-lifecycle) を参照

---

## 1. 基本ルール

- すべてのライフサイクルフックは **setup()の同期中** に登録する必要がある
- サーバーサイドレンダリング（SSR）中は一部フックが**呼ばれない**（onMountedなど）
- クリーンアップ（removeEventListener、clearIntervalなど）は**onUnmounted**で行う

---

## 2. 主要なライフサイクル一覧

| フック名           | タイミング                     | 主な用途                      | 備考                   |
| :----------------- | :----------------------------- | :---------------------------- | :--------------------- |
| `onBeforeMount`    | マウント直前                   | 最低限の初期セットアップ      | DOMはまだない          |
| `onMounted`        | マウント直後                   | 初期データロード、DOM操作開始 |       |
| `onBeforeUpdate`   | 再描画直前                     | 更新前の状態保持など          | 状態変更OK             |
| `onUpdated`        | 再描画直後                     | 更新後のDOM操作               | 無限ループに注意       |
| `onBeforeUnmount`  | アンマウント直前               | クリーンアップ準備            | インスタンスはまだ有効 |
| `onUnmounted`      | アンマウント後                 | 完全な後処|

---

## 3. 実用パターン例

### 3.1 初期データロード（onMounted）

```ts
import { ref, onMounted } from 'vue';

const users = ref([]);

onMounted(async () => {
  users.value = await fetchUserList();
});
```
- 初回API通信、初期値セットはonMounted
- DOM要素へのアクセスもこのタイミングでOK

### 3.2 画面更新後の処理（onUpdated）
コンポーネントのDOMツリーが更新された後に呼び出される。コールバックを登録。

```vue
<script setup>
import { ref, onUpdated } from 'vue'

const count = ref(0)

// onUpdatedは、JavaScriptでいう「イベントリスナー(onUpdateイベントを監視)」のようなもの。
// コンポーネントのリアクティブな状態が変わってDOMが更新された後に、自動的に呼び出される。
onUpdated(() => {
  console.log(count.value)
})
</script>

<template>
  <button id="count" @click="count++">{{ count }}</button>
</template>
```

### 3.3  リソースのクリーンアップ（onUnmounted）
コンポーネントがアンマウントされた後に呼び出される。コールバックを登録。
```vue
<script setup>
import { onMounted, onUnmounted } from 'vue'

let intervalId: number

// onMountedでタイマーを起動し、onUnmountedで確実にクリアする。
// イベントリスナーやタイマーのクリーンアップはここで行う。
onMounted(() => {
  intervalId = setInterval(() => {
    console.log('タイマー動作中')
  }, 1000)
})

onUnmounted(() => {
  clearInterval(intervalId)
})
</script>
```

### 3.4 DOM構築前の処理（onBeforeMount）
コンポーネントがマウントされる直前に呼び出される。コールバックを登録。
```vue
<script setup>
import { onBeforeMount } from 'vue'

// onBeforeMountは、JavaScriptでいう「初期描画前イベントリスナー」のようなもの。
// コンポーネントのDOMがまだ作成されていない状態で呼び出される。
onBeforeMount(() => {
  console.log('マウント直前：まだDOMは存在しない')
})
</script>
```

### 3.5 DOM更新前の処理（onBeforeUpdate）
コンポーネントのDOMツリーが更新される直前に呼び出される。コールバックを登録。
```vue
<script setup>
import { ref, onBeforeUpdate } from 'vue'

const count = ref(0)

// onBeforeUpdateは、JavaScriptでいう「更新前イベントリスナー」のようなもの。
// リアクティブな変更によるDOM更新の直前に呼び出される。
onBeforeUpdate(() => {
  console.log('更新前のカウント:', count.value)
})
</script>
```

### 3.6 破棄直前の処理（onBeforeUnmount）
コンポーネントがアンマウントされる直前に呼び出される。コールバックを登録。
```vue 
<script setup>
import { onBeforeUnmount } from 'vue'

// onBeforeUnmountは、JavaScriptでいう「破棄前イベントリスナー」のようなもの。
// アンマウント直前に、リソース解放準備などを行う。
onBeforeUnmount(() => {
  console.log('アンマウント直前の処理')
})
</script>
```