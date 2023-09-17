<script>
  import { QrcodeCapture } from 'vue3-qrcode-reader'
  import { QrcodeDropZone } from 'vue3-qrcode-reader'

  export default {
    components: {
      QrcodeCapture,
      QrcodeDropZone
    },
    props: {
      modelValue: {
        type: String
      }
    },
    emits: [
      'update:modelValue'
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
    data: function() {
      return {
        dragover: false,
        error: null,
        fileInput: null
      }
    },
    methods: {
      onDecode: function(data) {
        this.localData = data;
      },

      onDragOver: function(isDraggingOver) {
        this.dragover = isDraggingOver;
      },

      onDetect: async function(detectedCodes) {
        try {
          const result = await detectedCodes;
          this.localData = result.content;
        } catch (e) {
          console.log(e);
        }
      },

      logErrors: function(error) {
        if (error.name === 'DropImageFetchError') {
          this.error = "Sorry, you can't load cross-origin images :/";
        } else if (error.name === 'DropImageDecodeError') {
          this.error = "Ok, that's not an image. That can't be decoded.";
        } else {
          this.error = `Ups, what kind of error is this?!${ error.message }`;
        }
      },
    }
  }
</script>

<template>
  <qrcode-drop-zone
    class="drag-drop__container"
    :class="{ 'dragover' : dragover }"
    @decode="onDecode"
    @dragover="onDragOver"
    @error="logErrors">
    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 16 16">
      <path fill="currentColor" d="M8 2a5.53 5.53 0 0 0-3.594 1.342c-.766.66-1.321 1.52-1.464 2.383C1.266 6.095 0 7.555 0 9.318C0 11.366 1.708 13 3.781 13h8.906C14.502 13 16 11.57 16 9.773c0-1.636-1.242-2.969-2.834-3.194C12.923 3.999 10.69 2 8 2zm2.354 5.146a.5.5 0 0 1-.708.708L8.5 6.707V10.5a.5.5 0 0 1-1 0V6.707L6.354 7.854a.5.5 0 1 1-.708-.708l2-2a.5.5 0 0 1 .708 0l2 2z" />
    </svg>
    <div class="uploading-mode__container">
      <p>Drag & Drop to Upload Image</p>
      <p>OR</p>
      <qrcode-capture
        capture="environment"
        ref="fileInput"
        @detect="onDetect">
      </qrcode-capture>
    </div>
  </qrcode-drop-zone>
</template>

<style scoped>
  .drag-drop__container {
    background-color: #e9ecef;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: .5rem;
    padding: .75rem;
    height: 100%;
    widows: 100%;
    
    & svg {
      width: 4rem;
      height: 4rem;
    }

    & .uploading-mode__container {
      width: 100%;
      text-align: center;
      
      & p {
        margin-bottom: .5rem;
      }
    }
  }
  .drag-drop__container.dragover {
    background-color: #ccc;
  }
</style>
