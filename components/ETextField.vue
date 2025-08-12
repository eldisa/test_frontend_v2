<template>
  <div class="e-text-field">
    <label v-if="label" :for="fieldId" class="e-text-field__label">
      {{ label }}
    </label>
    <input
      :id="fieldId"
      :value="value"
      :type="type"
      :placeholder="placeholder"
      :required="required"
      :min="min"
      :max="max"
      class="e-text-field__input"
      @input="handleInput"
      @blur="handleBlur"
    />
    <div v-if="errorMessage" class="e-text-field__error">
      {{ errorMessage }}
    </div>
  </div>
</template>

<script setup lang="ts">
import { withDefaults, defineProps, defineEmits, computed } from 'vue'

interface Props {
  id?: string
  label?: string
  value?: string | number // 修改為同時接受 string 和 number
  type?: 'text' | 'number' | 'email'
  placeholder?: string
  required?: boolean
  min?: number
  max?: number
  errorMessage?: string
}

interface Emits {
  (event: 'update:value', value: string | number): void
}

const props = withDefaults(defineProps<Props>(), {
  type: 'text',
  required: false,
})

const emit = defineEmits<Emits>()

// 生成唯一 ID
const fieldId = computed(() => {
  if (props.id) return props.id
  return `field-${Math.random().toString(36).substr(2, 9)}`
})

const handleInput = (event: Event) => {
  const target = event.target as HTMLInputElement
  let value: string | number = target.value

  if (props.type === 'number') {
    value = target.value === '' ? '' : Number(target.value)
  }

  emit('update:value', value)
}

const handleBlur = (event: Event) => {
  const target = event.target as HTMLInputElement
  if (props.type === 'number' && target.value === '') {
    emit('update:value', 0)
  }
}
</script>

<style scoped lang="scss">
.e-text-field {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;

  &__label {
    font-size: 0.875rem;
    font-weight: 500;
    color: white;
  }

  &__input {
    padding: 0.75rem;
    border: 1px solid #d1d5db;
    border-radius: 0.5rem;
    font-size: 1rem;
    transition: all 0.2s ease-in-out;

    &:focus {
      outline: none;
      border-color: #3b82f6;
      box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
    }

    &:hover {
      border-color: #9ca3af;
    }

    &[type='number'] {
      &::-webkit-outer-spin-button,
      &::-webkit-inner-spin-button {
        -webkit-appearance: none;
        margin: 0;
      }
    }
  }

  &__error {
    font-size: 0.875rem;
    color: #ef4444;
  }
}
</style>
