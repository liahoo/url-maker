<template>
  <div>
    <b-navbar toggleable="lg" type="dark" variant="primary">
      <b-navbar-brand href="/">
        <img src="/img/logo.png" class="nav-img-logo" alt="logo">
      </b-navbar-brand>

      <b-navbar-toggle target="nav-collapse" />

      <b-collapse id="nav-collapse" is-nav>
        <!-- Right aligned nav items -->
        <b-navbar-nav class="ml-auto">
          <b-nav-item-dropdown text="Scanner" right>
            <b-dropdown-item href="/scanner/qrcode">
              QR Code
            </b-dropdown-item>
            <b-dropdown-item href="/scanner/urlstring">
              Url String
            </b-dropdown-item>
            <b-dropdown-item href="/scanner/imagefile">
              Image File
            </b-dropdown-item>
          </b-nav-item-dropdown>
          <b-nav-item-dropdown text="Verify" right>
            <b-dropdown-item href="/verify/universallink">
              Unversal Link
            </b-dropdown-item>
            <b-dropdown-item href="/verify/applink">
              App Link
            </b-dropdown-item>
          </b-nav-item-dropdown>
          <b-nav-item-dropdown text="Tools" right>
            <b-dropdown-item href="/tools/json/stringifier">
              JSON Stringifier
            </b-dropdown-item>
            <b-dropdown-item href="/tools/json/parser">
              JSON Parser
            </b-dropdown-item>
            <b-dropdown-item href="/tools/base64/encoder">
              Base64 Encoder
            </b-dropdown-item>
            <b-dropdown-item href="/tools/base64/decoder">
              Base64 Decoder
            </b-dropdown-item>
            <b-dropdown-item href="/tools/s2s/event">
              S2S Event
            </b-dropdown-item>
            <b-dropdown-item href="/tools/s2s/webevent">
              Web S2S Event
            </b-dropdown-item>
          </b-nav-item-dropdown>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
  </div>
</template>

<script>
export default {

}
</script>

<style>
.navbar {
  /* background-color: rgba(0, 128, 212, 1) !important; */
  align-items: center;
  padding: 0.25rem 0.25rem;
}
.navbar .navbar-brand {
  font-size: 14px;
  font-weight: bolder;
  flex-direction: row;
}

.navbar .navbar-brand .nav-img-logo {
  widows: 150px;
  height: 25px;
}
</style>
