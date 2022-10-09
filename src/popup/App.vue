<template>
  <div class="app">
    <div class="app-main">
      <div class="app-main-btn">
        <a-input v-model:value="todoItem.context" size="small" placeholder="添加ToDo" />
        <a-button shape="circle" size="small" @click="addItem">
          <template #icon>
            <check-outlined />
          </template>
        </a-button>
      </div>
      <div class="app-main-todolist">
        <a-checkbox v-for="item in todoList" :key="item.context" v-model:checked="item.state" style="margin-left: 8px"
                    @change="onCheckChange">
          <div class="app-main-todolist-item">
            <span class="app-main-todolist-item-text">{{ item.context }}</span>
            <a-button shape="circle" size="small" @click="deleteItem(item)" class="app-main-todolist-item-btn">
              <template #icon>
                <close-outlined />
              </template>
            </a-button>
          </div>
        </a-checkbox>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { CheckOutlined, CloseOutlined } from "@ant-design/icons-vue";
import { reactive, ref } from "vue";

interface todoInterface {
  state: boolean;
  context: string;
}

const todoList = ref<todoInterface[]>([]);
const todoItem = reactive<todoInterface>({ state: false, context: "" });

chrome.storage.sync.get(["todoList"], function(val: any) {
  for (let i in val.todoList) {
    todoList.value.push(val.todoList[i]);
  }
});

const addItem = () => {
  todoList.value.unshift({ ...todoItem });
  chrome.storage.sync.set({
    "todoList": todoList.value
  });
  todoItem.context = "";
};
const deleteItem = (item: todoInterface) => {
  todoList.value = todoList.value.filter((value: todoInterface) => value !== item);
  chrome.storage.sync.set({
    "todoList": todoList.value
  });
};
const onCheckChange = () => {
  chrome.storage.sync.set({
    "todoList": todoList.value
  });
};
</script>

<style lang="less" scoped>
//@import "../style/popup.less";
.app {
  width: 300px;
  height: 200px;
  //overflow: auto;
  //padding-bottom: 12px;
  //上半部分
  .app-main-btn {
    //padding-top: 12px;
    //padding-left: 8px;
    padding: 8px;
    display: flex;
    align-items: center;
    //输入框
    .ant-input {
      background-color: rgb(241, 243, 244);
      border: 1px solid rgb(241, 243, 244);
      margin-right: 8px;
    }
  }

  //下部分列表
  .app-main-todolist {
    display: flex;
    flex-direction: column;
    padding-bottom: 8px;

    .ant-checkbox-wrapper-checked {
      text-decoration: line-through;
      color: rgb(191, 191, 191);
    }

    .ant-checkbox-checked .ant-checkbox-inner {
      background-color: rgb(191, 191, 191);
      border-color: rgb(191, 191, 191);
    }

    .app-main-todolist-item {
      //width: 100%;
      //display: flex;
      //justify-content: space-around;

      .app-main-todolist-item-text {
        //width: 240px;
      }

      .ant-btn {
        margin-left: 12px;
        min-width: 20px;
        width: 20px;
        height: 20px;
      }
    }

    .app-main-todolist-item-btn {
      //float: right;
    }
  }
}
</style>
