<template>
  <button :class="classes" class="ui-button">
    <span class="ui-button__content">
      <slot />
    </span>
  </button>
</template>

<script setup lang="ts">
import {computed} from "vue";

type Props = {
  appearance?: 'primary' | 'secondary' | 'text'
}

const props = withDefaults(defineProps<Props>(), {
  appearance: 'primary'
})

const classes = computed(() => ['ui-button', `ui-button--${props.appearance}`])
</script>

<style lang="scss" scoped>
.ui-button {
  line-height: 24px;
  padding: 15px 20px;
  font-size: 18px;
  font-weight: 500;
  border-radius: 5px;
  border: none;
  cursor: pointer;
  transition: background-color .15s ease-in-out;

  &:disabled {
    pointer-events: none;
  }

  &--primary {
    background-color: #eab676;
    color: #eee;

    &:disabled {
      background-color: #d5a263;
    }

    &:hover {
      background-color: #d5a263;
    }

    &:active {
      background-color: #b88e5a;
    }
  }

  &--secondary {
    background-color: #1e81b0;
    color: #eee;

    &:disabled {
      background-color: #106b97;
    }

    &:hover {
      background-color: #106b97;
    }

    &:active {
      background-color: #0a4b6b;
    }
  }

  &--text {
    padding: 0;
    border-radius: 0;
  }

  &__content {
    white-space: nowrap;
  }

  :deep(svg) {
    font-size: 24px;
  }
}
</style>
