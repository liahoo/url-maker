<template>
  <div>
    <p v-if="error" class="error">
      Error: {{ error }}
    </p>
    <div v-if="result" class="m-1">
      <div>Result:</div>
      <a :href="result">{{ result }}</a>
      <br>
      <div>
        <b-button>
          Copy
        </b-button>
        <b-button :href="result" variant="outline-primary">
          Open
        </b-button>
        <b-button :href="result" target="_blank" variant="outline-primary">
          Open(New)
        </b-button>
        <b-button variant="success">
          <router-link :to="'/?url='+result">
            <span class="parse">Parse</span>
          </router-link>
        </b-button>
      </div>
    </div>
    <qrcode-stream :camera="camera" @decode="onDecode" @init="onInit" />
  </div>
</template>

<script>
import { QrcodeStream } from 'vue-qrcode-reader'
export default {
  name: 'QrcodeReader',
  components: {
    QrcodeStream
  },
  data () {
    return {
      result: '',
      error: '',
      camera: 'auto'
    }
  },
  methods: {
    onDecode (result) {
      this.result = result
      this.camera = 'off'
    },
    async onInit (promise) {
      try {
        await promise
      } catch (error) {
        if (error.name === 'NotAllowedError') {
          this.error = 'ERROR: you need to grant camera access permisson'
        } else if (error.name === 'NotFoundError') {
          this.error = 'ERROR: no camera on this device'
        } else if (error.name === 'NotSupportedError') {
          this.error = 'ERROR: secure context required (HTTPS, localhost)'
        } else if (error.name === 'NotReadableError') {
          this.error = 'ERROR: is the camera already in use?'
        } else if (error.name === 'OverconstrainedError') {
          this.error = 'ERROR: installed cameras are not suitable'
        } else if (error.name === 'StreamApiNotSupportedError') {
          this.error = 'ERROR: Stream API is not supported in this browser'
        }
      }
    }

  }

}
</script>

<style>
.error {
  color: red;
}
span.parse {
 color: white;
}
</style>
