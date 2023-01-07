<script>
import { onMounted, ref, reactive, computed } from "vue";

export default {
  setup() {
    const memos = ref([]);
    const data = reactive({
      title: "",
      content: "",
      edit: false,
      id: null,
    });
    const showForm = ref(false);

    const reset = () => {
      data.title = "";
      data.content = "";
      showForm.value = false;
    };

    const select = (memo) => {
      data.edit = true;
      data.title = memo.title;
      data.content = memo.content;
      data.id = memo.id;
      showForm.value = true;
    };

    const addMemo = () => {
      if (data.title !== "" && !data.edit) {
        const memo = {
          id: Math.floor(Math.random() * 1000),
          title: data.title,
          content: data.content,
        };
        memos.value.push(memo);
      } else {
        memos.value.forEach(function (memo, index) {
          if (memos.value[index].id === data.id) {
            memo.title = data.title;
            memo.content = data.content;
            data.edit = false;
          }
        });
      }
      reset();
      saveMemo();
    };

    const deleteMemo = (id) => {
      if (data.edit) {
        memos.value = memos.value.filter((memo) => {
          return memo.id !== id;
        });
        data.edit = false;
        reset();
        saveMemo();
      }
    };
    const saveMemo = () => {
      const json = JSON.stringify(memos.value);
      localStorage.setItem("memo", json);
    };
    const allMemos = () => {
      const json = localStorage.getItem("memo");
      if (json !== null) {
        memos.value = JSON.parse(json);
      }
      return memos.value;
    };

    const changeButtonName = computed(() =>
      data.edit === false ? "保存" : "更新"
    );

    onMounted(() => {
      allMemos();
    });
    return {
      memos,
      data,
      select,
      addMemo,
      deleteMemo,
      showForm,
      changeButtonName,
    };
  },
};
</script>

<template>
  <div class="container">
    <div>
      <ul>
        <li
          v-for="memo in memos"
          v-bind:key="memo.id"
          @click="select(memo)"
          class="list"
        >
          {{ memo.title }}
        </li>
      </ul>
    </div>
    <div class="btn-right">
      <button @click="showForm = true" class="btn">+</button>
    </div>
    <div v-if="showForm" class="form">
      <div>
        <label class="title">タイトル</label>
        <input type="text" name="title" v-model="data.title" />
      </div>
      <div>
        <label>内容</label>
        <textarea
          class="memo-contents"
          name="contents"
          v-model="data.content"
        ></textarea>
      </div>
      <div class="btn-right">
        <button class="btn" @click="addMemo">{{ changeButtonName }}</button>
        <button class="btn" v-if="data.edit" @click="deleteMemo(data.id)">
          削除
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  width: 400px;
}

ul {
  margin-top: 10px;
}

.list:hover {
  color: #9dbbff;
  cursor: pointer;
}

.btn-right {
  text-align: right;
}

.btn {
  background-color: #333;
  color: #fff;
  width: 80px;
  border-radius: 5px;
  padding: 18px, 32px;
  margin-bottom: 10px;
  margin-left: 10px;
}

label {
  width: 100px;
  display: inline-block;
  text-align: right;
  vertical-align: middle;
  padding-right: 8px;
}

input[type="text"],
textarea {
  width: 300px;
  margin-bottom: 10px;
  border: solid 1px #aaa;
  border-radius: 4px;
  outline-color: #9dbbff;
  vertical-align: middle;
}
</style>
