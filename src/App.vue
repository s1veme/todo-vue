<template>
  <div class="app">
    <div class="container">
      <header class="app__header">
        <h1>Список дел</h1>
      </header>

      <div class="app__content">
        <form @submit.prevent>
          <div class="app__content-top">
            <ui-input v-model="title" />

            <ui-button @click="handleTaskAdd" :disabled="!title">
              Создать
            </ui-button>

            <ui-button appearance="secondary" :disabled="!tasks.length" @click="handleTasksClear">
              Отчистить
            </ui-button>
          </div>

          <div class="tasks-container">
            <div class="app__content-tasks">
              <transition-group name="todo">
                <todo-task
                  v-for="task in tasks"
                  :key="task.uuid"
                  :task="task"
                  @complete="handleTaskComplete"
                  @remove="handleTaskRemove"
                />
              </transition-group>
            </div>

            <hr />

            <div class="app__content-summary">
              <div>
                Сделано ({{ allCompletedCount }})
              </div>

              <div>
                Не сделано ({{ uncompletedCount }})
              </div>

              <div>
                <todo-filter :sort="localSort" @sort="handleSort">
                  <template #trigger>
                    Все ({{ allCount }})

                    <svg width="1em" height="1em" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                      <path d="M16 18L16 6M16 6L20 10.125M16 6L12 10.125" stroke="#1C274C" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
                      <path d="M8 6L8 18M8 18L12 13.875M8 18L4 13.875" stroke="#1C274C" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
                    </svg>
                  </template>
                </todo-filter>
              </div>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import {computed, ref} from "vue";
import {UiButton, UiInput} from "./components/shared";
import TodoTask from "./components/todo/task/TodoTask.vue";
import {SortType, TaskType} from "./types";
import TodoFilter from "./components/todo/sort/TodoSort.vue";

const tasks = ref<Array<TaskType>>(JSON.parse(localStorage.getItem('tasks') ?? '[]'))
const title = ref('')
const localSort = ref<SortType>('alphaAsc')

const allCompletedCount = computed(() => tasks.value.filter((task) => task.status === 'done').length)
const uncompletedCount = computed(() => tasks.value.filter((task) => task.status === 'todo').length)
const allCount = computed(() => tasks.value.length)

const setLocalStorageTasks = () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value))
}

const handleSort = (sort: SortType) => {
  localSort.value = sort

  switch (sort) {
    case 'alphaAsc': {
      tasks.value.sort((a, b) => a.title.localeCompare(b.title))

      break
    }

    case 'alphaDesc': {
      tasks.value.sort((a, b) => b.title.localeCompare(a.title))

      break
    }

    case 'newAsc': {
      tasks.value.sort((a, b) => new Date(a.createdAt).getTime() - new Date(b.createdAt).getTime())

      break
    }

    case 'newDesc': {
      tasks.value.sort((a, b) => new Date(b.createdAt).getTime() - new Date(a.createdAt).getTime())

      break
    }
  }
}

const handleTaskAdd = () => {
  tasks.value.push({
    title: title.value,
    createdAt: new Date().toString(),
    uuid: window.crypto.randomUUID(),
    status: 'todo',
  })

  title.value = ''

  setLocalStorageTasks()

  handleSort(localSort.value)
}

const handleTaskComplete = (task: TaskType) => {
  tasks.value = tasks.value.map((_task) => {
    if (_task.uuid === task.uuid) {
      return {
        ..._task,
        status: _task.status === 'todo' ? 'done' : 'todo',
      }
    }

    return _task
  })

  setLocalStorageTasks()
}

const handleTaskRemove = (task: TaskType) => {
  tasks.value = tasks.value.filter((_task) => _task.uuid !== task.uuid)

  setLocalStorageTasks()
}

const handleTasksClear = () => {
  tasks.value.length = 0

  setLocalStorageTasks()
}
</script>

<style lang="scss" scoped>
.app {
  &__header {
    padding: 20px 0;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  &__content {
    form {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      margin-top: 20px;
    }
  }

  &__content-top {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  &__content-summary {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-weight: 500;
  }
}

.tasks-container {
  max-width: 40%;
  display: flex;
  flex-direction: column;
  row-gap: 20px;
  width: 100%;
  margin-top: 40px;

  hr {
    height: 2px;
    background-color: #fff;
    outline: none;
  }
}

.todo-move,
.todo-enter-active,
.todo-leave-active {
  transition: all 0.5s ease;
}

.todo-enter-from,
.todo-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
</style>
