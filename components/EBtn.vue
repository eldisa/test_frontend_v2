<template>
  <button
    :class="[
      'e-btn',
      `e-btn-${color}`,
      {
        'e-btn-disabled': disabled,
      },
    ]"
    :disabled="disabled"
    @click="$emit('click', $event)"
  >
    {{ text || '' }}
    <slot v-if="!text" />
  </button>
</template>

<script setup lang="ts">
interface Props {
  text?: string
  color?: 'success' | 'error' | 'warn'
  disabled?: boolean
}

interface Emits {
  click: [event: MouseEvent]
}

const props = withDefaults(defineProps<Props>(), {
  color: 'success',
  disabled: false,
})

defineEmits<Emits>()
</script>

<style scoped lang="scss">
.e-btn {
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 0.5rem;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease-in-out;
  min-width: 100px;

  &:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  &-success {
    background-color: #10b981;
    color: white;

    &:hover:not(:disabled) {
      background-color: #059669;
      transform: translateY(-1px);
      box-shadow: 0 4px 12px rgba(16, 185, 129, 0.3);
    }

    &:active:not(:disabled) {
      background-color: #047857;
      transform: translateY(0);
      box-shadow: 0 2px 6px rgba(16, 185, 129, 0.3);
    }
  }

  &-error {
    background-color: #ef4444;
    color: white;

    &:hover:not(:disabled) {
      background-color: #dc2626;
      transform: translateY(-1px);
      box-shadow: 0 4px 12px rgba(239, 68, 68, 0.3);
    }

    &:active:not(:disabled) {
      background-color: #b91c1c;
      transform: translateY(0);
      box-shadow: 0 2px 6px rgba(239, 68, 68, 0.3);
    }
  }

  &-warn {
    background-color: #f59e0b;
    color: white;

    &:hover:not(:disabled) {
      background-color: #d97706;
      transform: translateY(-1px);
      box-shadow: 0 4px 12px rgba(245, 158, 11, 0.3);
    }

    &:active:not(:disabled) {
      background-color: #b45309;
      transform: translateY(0);
      box-shadow: 0 2px 6px rgba(245, 158, 11, 0.3);
    }
  }
}
</style>
