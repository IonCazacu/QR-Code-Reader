<script>
  import { QrcodeCapture } from 'vue3-qrcode-reader'
  import AppQrImageDragDrop from './AppQrImageDragDrop.vue'

  export default {
    components: {
      QrcodeCapture,
      AppQrImageDragDrop
    },
    props: {
      modelValue: {
        type: String
      }
    },
    emits: [
      'update:modelValue',
    ],
    computed: {
      localData: {
        get: function() {
          return this.modelValue;
        },
        set: function(value) {
          this.$emit('update:modelValue', value);
        }
      }
    },
  }
</script>

<template>
  <div class="qrcode-drop-zone__container">
    <app-qr-image-drag-drop v-model="localData"></app-qr-image-drag-drop>
  </div>
</template>

<style scoped>
  .qrcode-drop-zone__container {
    border: 1px dashed black;
    margin-bottom: 1rem;
    height: 248.65625px;
  }
</style>