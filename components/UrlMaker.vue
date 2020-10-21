<template>
  <div>
    <b-form>
      <b-form-radio-group
        id="link-type"
        v-model="linkType"
        :options="['Click', 'Impression', 'OneLink']"
        value="Click"
        class="mt-2"
      />
      <b-input-group class="mb-2">
        <b-form-input v-model="scheme" class="col-3 col-md-2" placeholder="scheme" value="https://" />
        <b-form-input v-if="linkType === 'OneLink'" v-model="subdomain" class="col-3" placeholder="subdomain" />
        <b-form-input placeholder="domain" :value="getHost()" />
      </b-input-group>
      <b-form-input v-model="path" :placeholder="linkType.indexOf('OneLink')>=0 ? 'OneLink ID' : 'App ID'" />
      <span>Parameters</span>
      <b-input-group
        v-for="(param, index) in params"
        :key="index"
        class="mt-2 mb-2"
      >
        <b-form-input v-model="param.name" list="param-name-list" laceholder="Key name" class="col-5" />
        <datalist id="param-name-list">
          <option v-for="paramName in getParamsUnselected()" :key="paramName">
            {{ paramName }}
          </option>
        </datalist>

        <b-form-input v-model="param.value" placeholder="Param Value" />
      </b-input-group>
    </b-form>
    <b-button variant="success" @click="addParam()">
      Add
    </b-button>
    <b-button variant="primary" @click="snapShot()">
      Snap Shot
    </b-button>
    <div>
      <a :href="generateUrl()">{{ generateUrl() }}</a>
    </div>
    <div v-for="(shot, index) in snapShots" :key="index" class="mt-2">
      <a :href="shot">{{ shot }}</a>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      scheme: 'https://',
      linkType: 'Click',
      subdomain: 'go',
      path: '',
      presetKeys: [
        'pid', 'c', 'af_channel', 'af_adset', 'af_ad', 'af_site_id', 'af_sub1', 'af_dp', 'af_android_url', 'af_ios_url', 'af_web_dp', 'Custom'
      ],
      params: [
        { name: 'pid' }
      ],
      urlResult: '',
      snapShots: []
    }
  },
  methods: {
    getHost () {
      switch (this.$data.linkType) {
        case 'Click': return 'app.appsflyer.com'
        case 'Impression': return 'impression.appsflyer.com'
        case 'OneLink': return '.onelink.me'
        case 'OneLink(CN)': return '.onelnk.com'
        default:
          return ''
      }
    },
    getParamsUnselected () {
      return this.presetKeys.filter(key => !this.params.map(obj => obj.name).includes(key))
    },
    addParam () {
      this.$data.params.push({ name: '', value: '' })
    },
    generateUrl () {
      let url = (this.linkType.includes('OneLink') ? this.subdomain : '') + this.getHost() + '/' + this.path
      url = url.replace('//', '/')
      url = this.scheme + url
      if (this.params.length > 0) {
        url += '?' + this.params.flatMap(obj => obj.name + '=' + obj.value).join('&')
      }
      return url
    },
    snapShot () {
      this.snapShots.push(this.generateUrl())
    }
  }
}
</script>

<style>

</style>
