<template>
  <div :class="todoTaskClasses" class="todo-task">
    <div class="todo-task__left">
      <ui-button appearance="text" class="todo-task__complete-icon" @click="emits('complete', props.task)">
        <svg width="1em" height="1em" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M4 12.6111L8.92308 17.5L20 6.5" stroke="#000000" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </ui-button>

      <div>
        <p class="todo-task__title">
          {{ props.task.title }}
        </p>

        <p class="todo-task__date">от {{ formattedDate }}</p>
      </div>
    </div>

    <ui-button appearance="text" @click="emits('remove', props.task)">
      <svg width="1em" height="1em" viewBox="-0.5 0 25 25" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path d="M3 21.32L21 3.32001" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
        <path d="M3 3.32001L21 21.32" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
      </svg>
    </ui-button>
  </div>
</template>

<script setup lang="ts">
import type {TaskType} from "../../../types";
import {computed} from "vue";
import { UiButton } from "../../shared";

type Emits = {
  (event: 'remove', task: TaskType): void
  (event: 'complete', task: TaskType): void
}

const props = defineProps<{ task: TaskType }>()
const emits = defineEmits<Emits>()

const todoTaskClasses = computed(() => ['todo-task', `todo-task--${props.task.status}`])
const formattedDate = computed(() => new Date(props.task.createdAt).toISOString().slice(0, 10))
</script>

<style lang="scss" scoped>
.todo-task {
  $root: &;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 15px;
  margin: 0 auto 15px;
  border-radius: 5px;
  background-color: #fff;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, .1), 0 4px 6px -2px rgba(0, 0, 0, .05);
  transition: background-color .15s ease-in-out;

  &__left {
    display: flex;
    align-items: center;
    cursor: pointer;
  }

  &__complete-icon {
    margin-right: 12px;
    font-size: 24px;
  }

  &__date {
    font-size: 12px;
    margin-top: 2px;
  }

  &--done {
    background-color: #d1d1d1;

    #{$root}__title {
      text-decoration: line-through;
    }
  }
}
</style>
