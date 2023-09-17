<script>
  import AppQrCodeStream from '../components/AppQrCodeStream.vue'
  import AppQrImageUploadReader from '../components/AppQrImageUploadReader.vue'

  export default {
    components: {
      AppQrCodeStream,
      AppQrImageUploadReader,
    },
    data: function() {
      return {
        data: null,
        stream: {
          is_streaming: true
        },
      }
    },
    computed: {
      getQReadMethodText: function() {
        let qrReadMethodText = 'Scan an Image File';
        if (!this.stream.is_streaming) {
          qrReadMethodText = 'Scan using camera directly';
        }
        return qrReadMethodText;
      }
    },
    methods: {
      toggleQReadMode: function() {
        this.stream.is_streaming = !this.stream.is_streaming;
      },

      clearData: function() {
        this.data = null;
      },

      getLinkInformations: function() {
        let postData = {
          key: '519cdbafa06d35dadf99bf7cdffd0202', // API key
          q: this.data // qeury = URL
        };

        fetch('https://api.linkpreview.net', {
          method: 'POST',
          mode: 'cors',
          body: JSON.stringify(postData)
        })
        .then(result => {
          if (result.status !== 200) {
            console.log(result.status);
            throw new Error('Something went wrong');
          }
          return result.json();
        })
        .then(response => {
          this.link.data = response;
          console.log(response);
        })
        .catch(error => {
          console.log(error);
        });
      }
    }
  }
</script>

<template>
  <main class="main">
    <div class="container">
      <div class="header__content">
        <div class="qr-reader">
          <div class="qr-reader__info">
            <h2>QR Code Reader</h2>
            <p>A QR code is a type of barcode that can be read easily by a digital device and which stores information as a series of pixels in a square-shaped grid.</p>
          </div>
        </div>
        <div class="qr-poster">
          <img src="../assets/qr-code-scan-poster.png" alt="qr code poster">
        </div>
      </div>
      
      <div class="body__content">
        <app-qr-code-stream
          v-if="this.stream.is_streaming"
          v-model="this.data">
        </app-qr-code-stream>
        
        <app-qr-image-upload-reader
          v-else
          v-model="this.data">
        </app-qr-image-upload-reader>
        
        <div class="read-mode__container">
          <button
            v-if="stream"
            type="button"
            class="btn btn-link"
            @click="this.toggleQReadMode">
            {{ this.getQReadMethodText }}
          </button>
        </div>
  
        <div v-if="this.data" class="data__container">
          <div class="data-result">{{ this.data }}</div>
          <button class="" @click="clearData">Clear</button>
        </div>
      </div>

    </div>
  </main>
</template>

<style scoped>
  .main {
    padding: 1rem;
    display: flex;
    flex-direction: column;
    flex-grow: 1;
    margin-top: 1rem;
    width: 100%;
    
    & .container {
      display: flex;
      flex-direction: column;
      align-items: center;
      flex-grow: 1;
    
      & .header__content {
        display: flex;
        justify-content: center;
        align-items: center;
        gap: 1.5rem;
        flex-grow: 1;
        margin-top: auto;
        margin-bottom: 3rem;
        
        & .qr-reader {
          width: 66%;
          height: 100%;
        
          & .qr-reader__info {
            position: relative;
            margin-bottom: 1.5rem;
            
            &::before {
              content: '';
              position: absolute;
              height: 100%;
              width: 6px;
              background-color: #363636;
            }
            
            & h2,
            & p {
              margin-left: .75rem;
            }
            
            & h2 {
              font-weight: bold;
              margin-bottom: .75rem;
            }

            & p:not(:last-child) {
              margin-bottom: .75rem;
            }
          }
        }

        & .qr-poster {
          width: 33%;
          height: 100%;
          display: flex;
          
          & img {
            width: 100%;
            height: auto;
          }
        }
        
        @media (max-width: 576px) {
          flex-direction: column-reverse;

          & .qr-reader {
            width: 100%;
          }
          
          & .qr-poster {
            width: 100%;
            align-items: center;
            justify-content: center;
        
            & img {
              width: 50%;
            }
          }
        }
      }

      & .body__content {
        width: 66%;

        & .read-mode__container {
          margin-bottom: 1.5rem;
          width: 100%;
          text-align: center;
        }
      
        & .data__container .data-result {
          background-color: #e9ecef;
          border: 1px solid #ced4da;
          margin-bottom: .75rem;
          padding: 10px 15px;
          font-size: 14px;
          outline: none;
          resize: none;
          width: 100%;
        }

        @media (max-width: 576px) {
          width: 100%;
        }
      }
    }
  }
</style>
