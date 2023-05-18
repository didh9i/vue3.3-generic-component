<template>
  <div
    ref="anchor"
    class="generic-select"
    :data-open="open"
    @click="toggle"
  >
    <span
      v-if="display"
      class="generic-select__value"
    >
      {{ display }}
    </span>
    <span
      v-else-if="placeholder"
      class="generic-select__placeholder"
    >
      {{ placeholder }}
    </span>
    <div
      ref="popover"
      class="generic-select__popover"
    >
      <div
        v-for="option of options"
        :key="option[idKey]"
        class="generic-select__option"
        @click="emit('update:model-value', option)"
      >
        {{ option[displayKey] }}
      </div>
    </div>
  </div>
</template>

<script
  setup
  lang="ts"
  generic="GIdKey extends string = 'id', GDisplayKey extends string = 'display',
  GValueKey extends string = 'value', GValue extends unknown"
>
import { computed, onMounted, ref, watch } from 'vue'
import { createPopper } from '@popperjs/core/lib/popper-lite.js'
import preventOverflow from '@popperjs/core/lib/modifiers/preventOverflow.js'
import flip from '@popperjs/core/lib/modifiers/flip.js'

interface IOption {
  [GIdKey]: string | number,
  [GDisplayKey]: GValue,
  [GValueKey]: string
}

const props = withDefaults(defineProps<{
  idKey?: GIdKey,
  displayKey?: GDisplayKey,
  valueKey?: GValueKey,
  modelValue: IOption | null,
  options: Array<IOption<T>>,
  placeholder: string
}>(), {
  idKey: 'id',
  displayKey: 'display',
  valueKey: 'value',
  placeholder: ''
})

const emit = defineEmits<{
  (e: 'update:model-value', value: IOption)
}>()

const open = ref(false)

function toggle () {
  open.value = !open.value
}

function globalClose(event: PointerEvent) {
  let current: HTMLElement = event.target

  while (current) {
    if (current === anchor.value) return
    current = current.parentElement
  }

  open.value = false;
}

let hasListener = false

watch(open, () => {
  if (open.value) {
    hasListener = true
    addEventListener('pointerdown', globalClose, { once: true })
  } else if (hasListener) {
    removeEventListener('pointerdown', globalClose)
  }
})

const display = computed<string>(() => props.modelValue?.[props.displayKey] || '')

const popover = ref<HTMLElement>()
const anchor = ref<HTMLElement>()

defineExpose({
  open
})

onMounted(() => {
  if (popover.value && anchor.value) {
    createPopper(anchor.value, popover.value, {
      placement: 'bottom',
      modifiers: [preventOverflow, flip]
    })
  }
})
</script>

<style lang="scss">
.generic-select {
  position: relative;
  cursor: pointer;
  padding: 0.6em 1.2em;
  --background-color: #1a1a1a;
  transition: border-color 0.25s;

  &:hover {
    border-color: #646cff;
  }

  &:focus,
  &:focus-visible {
    outline: 4px auto -webkit-focus-ring-color;
  }

  &::after,
  &::before {
    position: absolute;
    display: block;
    content: '';
    border-radius: 4px;
    right: 1.2em;
    width: 12px;
    height: 4px;
    top: 50%;
    rotate: 45deg;
    background-color: currentcolor;
    transition: translate 0.25s;
  }

  &::before {
    rotate: 45deg;
    translate: -25% -1px;
  }

  &::after {
    rotate: -45deg;
    translate: 25% -1px;
  }

  &,
  &__popover {
    background-color: var(--background-color);
    border-radius: 8px;
    border: 1px solid transparent;
    font-size: 1em;
    font-weight: 500;
    font-family: inherit;
  }

  &__popover {
    opacity: 0;
    min-height: 2.4em;
    width: 100%;
  }

  &__option {
    cursor: pointer;
    padding: 0.6em 1.2em;
    transition: border-color 0.25s;
    border: 1px solid transparent;

    &:first-child {
      border-radius: 8px 8px 0 0;
    }

    &:last-child {
      border-radius: 0 0 8px 8px;
    }

    &:hover {
      border-color: #646cff;
    }

    &:focus,
    &:focus-visible {
      outline: 4px auto -webkit-focus-ring-color;
    }
  }

  &__option {
    padding: 0.6em 1.2em;
    background-color: #1a1a1a;
    transition: border-color 0.25s;
  }
  &[data-open=true] {
    &::before {
      rotate: 45deg;
      translate: 25% -1px;
    }

    &::after {
      rotate: -45deg;
      translate: -25% -1px;
    }
  }

  &[data-open=true] &__popover {
    opacity: 1;
  }
}
</style>