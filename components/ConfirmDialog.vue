<template>
  <dialog ref="dialogRef" class="confirm-dialog backdrop:bg-black backdrop:bg-opacity-50">
    <div class="dialog-content bg-gray-800 text-white p-6 rounded-lg max-w-md w-full">
      <h3 class="text-lg font-semibold mb-4">{{ title }}</h3>
      <p class="text-gray-300 mb-6">{{ message }}</p>
      <div class="flex justify-end gap-3">
        <EBtn variant="primary" @click="onCancel">
          {{ $t('cancel') }}
        </EBtn>
        <EBtn :variant="confirmVariant" @click="onConfirm">
          {{ $t('confirm') }}
        </EBtn>
      </div>
    </div>
  </dialog>
</template>

<script setup lang="ts">
import { ref, nextTick } from 'vue'
import EBtn from './EBtn.vue'

interface Props {
  title: string
  message: string
  confirmVariant?: 'primary' | 'success' | 'danger' | 'warning'
}

withDefaults(defineProps<Props>(), {
  confirmVariant: 'primary',
})

const emit = defineEmits<{
  confirm: []
  cancel: []
}>()

const dialogRef = ref<HTMLDialogElement | null>(null)

const show = async () => {
  await nextTick()
  dialogRef.value?.showModal()
}

const hide = () => {
  dialogRef.value?.close()
}

const onConfirm = () => {
  emit('confirm')
  hide()
}

const onCancel = () => {
  emit('cancel')
  hide()
}

defineExpose({
  show,
  hide,
})
</script>

<style scoped>
.confirm-dialog {
  border: none;
  border-radius: 8px;
  padding: 0;
  background: transparent;
}

.confirm-dialog::backdrop {
  background-color: rgba(0, 0, 0, 0.5);
}

.dialog-content {
  margin: auto;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
}
</style>
