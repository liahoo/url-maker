<template>
  <div>
    <b-form @submit.prevent="snapShot">
      <b-form-radio-group
        id="link-type"
        v-model="selectedLinkTypeName"
        :options="linkTypeNames"
        class="mt-2"
      />
      <b-input-group class="mb-1">
        <b-form-input v-model="linkType.scheme" class="col-3 col-md-2" placeholder="scheme" name="scheme" />
        <b-form-input v-model="linkType.subdomain" class="col-3 col-md-2" placeholder="subdomain" name="subdomain" />
        <b-form-input v-model="linkType.host" placeholder="Host" name="host" />
      </b-input-group>
      <b-form-input v-model="path" :placeholder="selectedLinkTypeName.indexOf('OneLink')>=0 ? 'OneLink ID' : 'App ID'" />
      <hr>
      <span>Parameters</span>
      <b-input-group
        v-for="(param, index) in params"
        :key="index"
        class="mt-2 mb-2"
      >
        <b-input-group-prepend is-text>
          <input :checked="!param.disabled" type="checkbox" aria-label="is added" @change="param.disabled=!param.disabled">
        </b-input-group-prepend>
        <b-form-input v-model="param.name" :name="param.name" list="param-name-list" laceholder="Key name" class="col-4" />
        <datalist id="param-name-list">
          <option v-for="paramName in getParamsUnselected()" :key="paramName">
            {{ paramName }}
          </option>
        </datalist>

        <b-form-input v-model="param.value" placeholder="Param Value" />
        <b-input-group-append>
          <b-dropdown text="" right>
            <b-dropdown-item-button @click="param.value=encodeURIComponent(param.value)">
              Encode
            </b-dropdown-item-button>
            <b-dropdown-item-button @click="param.value=decodeURIComponent(param.value)">
              Decode
            </b-dropdown-item-button>
            <b-dropdown-item-button @click="params.splice(index, 1)">
              Delete
            </b-dropdown-item-button>
          </b-dropdown>
        </b-input-group-append>
      </b-input-group>
      <div>
        <b-button variant="success" @click="addParam()">
          Add
        </b-button>
        <b-button type="submit" variant="primary">
          Snap Shot
        </b-button>
      </div>
    </b-form>
    <hr>
    <div>
      <a :href="generateUrl()">{{ generateUrl() }}</a>
    </div>
    <hr>
    <div v-for="(shot, index) in snapShots" :key="index" class="mt-2">
      <a :href="shot">{{ shot }}</a>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      linkTypes: [
        {
          name: 'Click',
          scheme: 'https://',
          subdomain: 'app',
          host: '.appsflyer.com'
        },
        {
          name: 'Impression',
          scheme: 'https://',
          subdomain: 'impression',
          host: '.appsflyer.com'
        },
        {
          name: 'OneLink',
          scheme: 'https://',
          subdomain: 'go',
          host: '.onelink.me'
        },
        {
          name: 'Other',
          scheme: 'https://',
          subdomain: '',
          host: ''
        }
      ],
      path: '',
      selectedLinkTypeName: 'Click',
      presetKeys: [
        'pid', 'c', 'af_channel', 'af_adset', 'af_ad', 'af_site_id', 'af_sub1', 'af_dp', 'af_android_url', 'af_ios_url', 'af_web_dp'
      ],
      params: [
        { name: 'pid', value: '', disabled: false }
      ],
      urlResult: '',
      snapShots: []
    }
  },
  computed: {
    linkType () {
      return this.linkTypes.find(lt => lt.name === this.selectedLinkTypeName)
    },
    linkTypeNames () {
      return this.linkTypes.map(lt => lt.name)
    }
  },
  created () {
    // this.linkType = this.linkTypes[0]
    // this.linkTypeNames = this.linkTypes.map(lt => lt.name)
  },
  methods: {
    changeLinkType () {
      this.linkType = this.linkTypes.find(lt => lt.name === this.selectedLinkTypeName)
    },
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
      this.$data.params.push({ name: '', value: '', disabled: false })
    },
    generateUrl () {
      const { scheme, subdomain, host } = this.linkType
      let url = subdomain + host + '/' + this.path
      url = url.replace('//', '/')
      url = scheme + url
      const validParams = this.params.filter(p => !p.disabled)
      if (validParams.length > 0) {
        url += '?' + validParams.flatMap(obj => obj.name + '=' + obj.value).join('&')
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
