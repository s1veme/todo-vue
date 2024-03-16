<template>
  <input type="text" class="ui-input" v-model="modelValue">
</template>

<script setup lang="ts">
const modelValue = defineModel<string>({
  type: String,
})
</script>

<style lang="scss" scoped>
.ui-input {
  height: 54px;
  padding: 0 20px;
  font-size: 18px;
  border-radius: 5px;
  border: 1px solid #eee;
  outline: none;
}
</style>
