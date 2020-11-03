<template>
  <div>
    <b-input-group prepend="Url" class="mt-3">
      <b-form-input v-model="extUrl" />
      <b-input-group-append>
        <b-button variant="success" @click="parseUrl(extUrl)">
          Parse
        </b-button>
        <b-button variant="dark">
          <router-link to="/scanner/qrcode">
            <b-icon-camera variant="light" />
          </router-link>
        </b-button>
      </b-input-group-append>
    </b-input-group>

    <b-form class="mt-2 mb-4" @submit.prevent="snapShot">
      <b-form-radio-group
        id="link-type"
        v-model="selectedLinkTypeName"
        :options="linkTypeNames"
        class="mt-4"
      />
      <b-input-group class="mb-1">
        <b-form-input v-model="selectedLinkType.scheme" class="col-3 col-md-2" placeholder="scheme" name="scheme" />
        <b-form-input v-model="selectedLinkType.subdomain" class="col-3 col-md-2" placeholder="subdomain" name="subdomain" />
        <b-form-input v-model="selectedLinkType.host" placeholder="Host" name="host" />
      </b-input-group>
      <b-form-input v-model="path" :placeholder="selectedLinkTypeName.indexOf('OneLink')>=0 ? 'OneLink ID' : 'App ID'" />
      <div class="mt-4">
        Parameters
      </div>
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
          Make
        </b-button>
      </div>
    </b-form>
    <div>
      <a :href="generateUrl()">{{ generateUrl() }}</a>
    </div>
    <hr>
    <div v-for="(shot, index) in snapShots" :key="index" class="mt-2">
      <a :href="shot">{{ shot }}</a>
      <QRCode :text="shot" />
    </div>
  </div>
</template>

<script>
import { BIconCamera } from 'bootstrap-vue'
import QRCode from './QRCode'
export default {
  components: {
    QRCode,
    BIconCamera
  },
  data () {
    return {
      extUrl: '',
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
      qrcodeObj: {},
      snapShots: []
    }
  },
  computed: {
    selectedLinkType () {
      return this.linkTypes.find(lt => lt.name === this.selectedLinkTypeName)
    },
    linkTypeNames () {
      return this.linkTypes.map(lt => lt.name)
    }
  },
  created () {
    const url = this.$route.query.url
    if (url && url.length > 0) {
      this.extUrl = url
      this.parseUrl(url)
    }
  },
  methods: {
    changeLinkType () {
      this.selectedLinkType = this.linkTypes.find(lt => lt.name === this.selectedLinkTypeName)
    },
    findLinkTypeByDomain (subdomain, host) {
      switch (host) {
        case '.appsflyer.com': return subdomain === 'app' ? 'Click' : 'Impression'
        case '.onelink.me': return 'OneLink'
        // case 'af-link.com': return subdomain === 'app' ? 'Click(CN)' : 'Impression(CN)'
        // case 'onelnk.com': return 'OneLink(CN)'
        default:
          return 'Other'
      }
    },
    getParamsUnselected () {
      return this.presetKeys.filter(key => !this.params.map(obj => obj.name).includes(key))
    },
    addParam () {
      this.$data.params.push({ name: '', value: '', disabled: false })
    },
    generateUrl () {
      const { scheme, subdomain, host } = this.selectedLinkType
      let url = subdomain + host + '/' + this.path
      url = url.replace('//', '/')
      url = scheme + url
      const validParams = this.params.filter(p => !p.disabled)
      if (validParams.length > 0) {
        url += '?' + validParams.flatMap(obj => obj.name + '=' + obj.value).join('&')
      }
      return url
    },
    makeQRCode (text) {
      return new QRCode('qrcode', {
        text,
        width: 200,
        height: 200,
        colorDark: '#0f0', // 绿色
        colorLight: '#fff',
        correctLevel: QRCode.CorrectLevel.H
      }).makeQRCode(text)
    },
    snapShot () {
      this.snapShots.unshift(this.generateUrl())
    },
    parseUrl (url) {
      if (url && url.length > 0) {
        const urlParts = url.split('?')
        const schemeSepIdx = url.indexOf('://')
        const scheme = schemeSepIdx > 0 ? urlParts[0].substring(0, schemeSepIdx + 3) : 'https://'
        const urlWithoutScheme = urlParts[0].substring(schemeSepIdx + 3)
        const domainSepIdx = urlWithoutScheme.indexOf('/')
        const domain = urlWithoutScheme.substring(0, domainSepIdx)
        const domainParts = domain.split('.')
        const subdomain = domainParts.length <= 2 ? '' : domainParts.slice(0, domainParts.length - 2).join('.')
        const host = domainParts.length <= 2 ? domain : '.' + domainParts.slice(domainParts.length - 2).join('.')
        this.selectedLinkTypeName = this.findLinkTypeByDomain(subdomain, host)
        this.selectedLinkType.scheme = scheme
        this.selectedLinkType.subdomain = subdomain
        this.selectedLinkType.host = host
        this.path = urlWithoutScheme.substring(domainSepIdx + 1)

        if (urlParts.length > 1) {
          this.params = urlParts[1].split('&').map((q) => {
            const query = q.split('=')
            return {
              name: query[0], value: query[1] || ''
            }
          })
        } else {
          this.params = []
        }
      }
    }
  }
}
</script>

<style>

</style>
