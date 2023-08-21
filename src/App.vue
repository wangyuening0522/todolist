/* eslint-disable */
<template>
  <section class="todoapp">
    <header class="header">
      <h1>简易记事本</h1>
      <input
        type="text"
        @keyup.enter="addTodo"
        v-model="newTodo"
        class="new-todo"
        placeholder="想干啥"
      />
    </header>
    <section class="main">
      <input
        class="toggle-all"
        type="checkbox"
        id="toggle-all"
        v-model="allDone"
      />
      <label for="toggle-all">完成后全部记录</label>
      <ul class="todo-list">
        <li class="todo" v-for="todo in filtertodo" :key="todo.id">
          <div class="view">
            <input type="checkbox" class="toggle" v-model="todo.completed" />
            <!-- 任务名 -->
            <label>{{ todo.title }}</label>
            <!-- 删除按钮 -->
            <button class="destroy" @click="removeTodo(todo)"></button>
          </div>
        </li>
      </ul>
    </section>
    <!-- 0假1真 有数字就为true，就显示 -->
    <footer class="footer" v-show="filtertodo.length">
      <span class="todo-count">
        <strong>{{ left }}</strong
        >left
        <!-- 剩余可做任务数 -->
      </span>
      <span class="todo-choose">
        <!-- 显示全部任务按钮 -->
        <button class="clear-completed todo-do" @click="showAll">All</button>
        <!-- 显示可做任务按钮 -->
        <button class="clear-completed todo-do" @click="showActive">
          Active
        </button>
        <!-- 显示已完成任务按钮 -->
        <button class="clear-completed todo-do" @click="showCompleted">
          Completed
        </button>
      </span>
      <!-- 清空已完成任务按钮 -->
      <button class="clear-completed" @click="removeTo">Clear completed</button>
    </footer>
  </section>
</template>

<script>
import { computed, reactive, toRefs } from "vue";
export default {
  setup() {
    const state = reactive({
      newTodo: "", //输入框的值
      todos: [
        //初始化数据
        { id: "1", title: "吃饭", completed: true },
        { id: "2", title: "睡觉", completed: false },
      ],
      filtertodo: [
        { id: "1", title: "吃饭", completed: true },
        { id: "2", title: "睡觉", completed: false },
      ],
    });
    /* 添加list的方法 */
    const addTodo = () => {
      //输入框空不加入列表
      if (!state.newTodo) return;
      //加入数据到筛选的数组中(末尾)
      state.filtertodo.push({
        id: state.filtertodo.length + 1,
        title: state.newTodo,
        completed: false,
      });
      //todos为了后面状态切换可以还原原数据
      state.todos.push({
        id: state.todos.length + 1,
        title: state.newTodo,
        completed: false,
      });
      state.newTodo = ""; //每次回车加完后重新使输入框的值为空
    };
    /* 全选作用 */
    const allDone = computed({
      get: function () {
        return remaining.value === 0; //全选中就返回ture
      },
      set: function (value) {
        state.filtertodo.forEach((todo) => {
          todo.completed = value;
        });
      },
    });
    /* 删除按钮 */
    const removeTodo = (item) => {
      //把点击的该项过滤出去,满足条件(自己写的)的都留下
      state.filtertodo = state.todos.filter((todo) => todo.id != item.id);
      state.todos = state.todos.filter((todo) => todo.id != item.id);
    };
    /* 统计剩余任务 */
    const remaining = computed(
      //留下false
      // ()=>state.todos.filter((todo)=>!todo.completed).length
      () => state.todos.filter((todo) => todo.completed == false).length
    );
    const left = computed(() => {
      return remaining.value;
    });
    /* 清除列表中已完成的任务 */
    const removeTo = () => {
      state.filtertodo = state.todos.filter((todo) => !todo.completed);
    };
    /* 显示全部任务 */
    const showAll = () => {
      state.filtertodo = state.todos;
    };
    /* 显示未完成任务 */
    const showActive = () => {
      state.filtertodo = state.todos.filter((todo) => !todo.completed);
    };
    /* 显示已完成任务 */
    const showCompleted = () => {
      state.filtertodo = state.todos.filter((todo) => todo.completed);
    };
    return {
      ...toRefs(state),
      addTodo,
      // allDone,
      removeTodo,
      left,
      removeTo,
      showAll,
      showActive,
      showCompleted,
    };
  },
};
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
