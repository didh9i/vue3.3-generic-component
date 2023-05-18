<template>
  <h1>{{ msg }}</h1>

  <div class="card">
    <button
      type="button"
      @click="count++"
    >
      count is {{ count }}
    </button>
    <p>
      Edit
      <code>components/HelloWorld.vue</code>
      to test HMR
    </p>
  </div>

  <div class="card">
    <generic-select
      v-model="value"
      :options="options"
      placeholder="📃 Select"
      @update:model-value="console.log('@update:model-value', $event)"
    />
  </div>

  <p>
    Check out
    <a
      href="https://vuejs.org/guide/quick-start.html#local"
      target="_blank"
    >create-vue
    </a>
    , the official Vue + Vite starter
  </p>
  <p>
    Install
    <a
      href="https://github.com/vuejs/language-tools"
      target="_blank"
    >
      Volar
    </a>
    in your IDE for a better DX
  </p>
  <p class="read-the-docs">
    Click on the Vite and Vue logos to learn more
  </p>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import GenericSelect from './GenericSelect.vue'

type Props = {
  msg: string
}

defineProps<Props>()

const count = ref(0)
const options = [{
  id: 0,
  display: '🥞 Pancake',
  value: 'value:pancake'
}, {
  id: 1,
  display: '🍿 Popcorn',
  value: 'value:popcorn'
}, {
  id: 2,
  display: '🍍 Pineapple',
  value: 'value:pineapple'
}]
const value = ref(null)
</script>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
