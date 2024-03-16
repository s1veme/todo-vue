<template>
  <div class="todo-filter">
    <button type="button" class="todo-filter__trigger" @click="handleVisibleToggle">
      <slot name="trigger" />
    </button>

    <transition>
      <div v-if="isVisible" class="todo-filter__content">
        <ul>
          <li @click="modelValue = 'newAsc'" :class="['todo-filter__sort', { active: modelValue === 'newAsc' }]">
            Сначала новые
          </li>
          <li @click="modelValue = 'newDesc'" :class="['todo-filter__sort', { active: modelValue === 'newDesc' }]">
            Сначала старые
          </li>
          <li @click="modelValue = 'alphaAsc'" :class="['todo-filter__sort', { active: modelValue === 'alphaAsc' }]">
            А-я
          </li>
          <li @click="modelValue = 'alphaDesc'" :class="['todo-filter__sort', { active: modelValue === 'alphaDesc' }]">
            Я-а
          </li>
        </ul>
      </div>
    </transition>
  </div>
</template>

<script setup lang="ts">
import {computed, onMounted, ref} from "vue";
import {SortType} from "../../../types";

type Props = {
  sort: SortType
}

type Emits = {
  (event: 'sort', sort: SortType): void
}

const props = withDefaults(defineProps<Props>(), {
  sort: 'alphaAsc'
})

const emits = defineEmits<Emits>()

const modelValue = computed<SortType>({
  get(){
    return props.sort
  },
  set(value) {
    emits('sort', value)
  }
})

const isVisible = ref(false)

onMounted(() => {
  const container = document.querySelector('#app') as HTMLElement

  container?.addEventListener('click', () => { isVisible.value = false }, { capture: true })
})


const handleVisibleToggle = () => {
  isVisible.value = !isVisible.value
}

</script>

<style lang="scss" scoped>
.todo-filter {
  position: relative;

  &__trigger {
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 4px;
  }

  &__content {
    position: absolute;
    right: 0;
    top: 30px;
    background-color: #fff;
    transform: translateX(100%);
  }

  &__sort {
    padding: 5px 10px;
    cursor: pointer;
    transition: background-color .15s ease-in-out;
    border-radius: 5px;
    font-size: 14px;
    color: #000;
    white-space: nowrap;

    &.active {
      background-color: #f0f0f0;
    }
  }

  :deep(svg) {
    font-size: 24px;
  }
}

.v-enter-active,
.v-leave-active {
  transition: opacity 0.15s ease;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}
</style>
