<script>
import AppMemo from "./components/AppMemo.vue";

export default {
  name: "App",
  components: {
    AppMemo,
  },
};
</script>

<template>
  <h1>MemoApp</h1>
  <p>『+』ボタンを押してメモを登録しよう！</p>
  <AppMemo />
</template>
