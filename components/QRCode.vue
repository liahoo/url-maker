<template>
  <div id="qrcode" ref="qrCodeDiv" class="qrcode" style="width: 200px;height: 200px" />
</template>

<script>
import QRcode from 'qrcodejs2'
export default {
  name: 'Qrcode',
  props: {
    text: {
      type: String,
      required: true
    }
  },
  mounted () {
    this.qrcode()
  },
  methods: {
    qrcode () {
      const qrCode = new QRcode(this.$refs.qrCodeDiv, {
        text: this.text,
        width: 200,
        height: 200,
        colorDark: '#333333',
        colorLight: '#FFFFFF'
      })
      return qrCode
    }
  }
}
</script>

<style>

</style>
