<script>
  import { QrcodeStream } from 'vue3-qrcode-reader'

  export default {
    components: {
      QrcodeStream
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
      returnData: {
        get: function() {
          return this.modelValue;
        },
        set: function(value) {
          this.$emit('update:modelValue', value);
        }
      }
    },
    methods: {
      paintOutline: function(detectedCodes, ctx) {
        const {
          topLeftCorner, topRightCorner, bottomRightCorner, bottomLeftCorner
        } = detectedCodes;

        const lastThreeCorners = [
          topRightCorner, bottomRightCorner, bottomLeftCorner
        ];

        ctx.strokeStyle = 'red';
        ctx.beginPath();
        ctx.moveTo(topLeftCorner['x'], topLeftCorner['y']);

        for (const { x, y } of lastThreeCorners) {
          ctx.lineTo(x, y);
        }

        ctx.lineTo(topLeftCorner['x'], topLeftCorner['y']);
        ctx.closePath();
        ctx.stroke();
      },
      
      onDetect: async function(detectedCode) {
        try {
          const result = await detectedCode;
          this.returnData = result.content;
        } catch (e) {
          console.log(e);
        }
      }
    }
  }
</script>

<template>
  <qrcode-stream :track="paintOutline" @detect="onDetect"></qrcode-stream>
</template>

<style scoped>
  .qrcode-stream-wrapper {
    background-color: #000;
    margin-bottom: 1.5rem;
    -moz-box-shadow:
      0 2px 1px -1px rgba(0, 0, 0, .2),
      0 1px 1px 0 rgba(0, 0, 0, .141),
      0 1px 3px 0 rgba(0, 0, 0, .122) !important;
    -webkit-box-shadow:
      0 2px 1px -1px rgba(0, 0, 0, .2),
      0 1px 1px 0 rgba(0, 0, 0, .141),
      0 1px 3px 0 rgba(0, 0, 0, .122) !important;
    box-shadow:
      0 2px 1px -1px rgba(0, 0, 0, .2),
      0 1px 1px 0 rgba(0, 0, 0, .141),
      0 1px 3px 0 rgba(0, 0, 0, .122) !important;
  }
</style>
