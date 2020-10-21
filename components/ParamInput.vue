<template>
  <b-input-group class="mt-2 mb-2">
    <b-form-select v-model="paramKey" :options="paramKeys" class="col-4" />
    <b-form-input v-if="paramKey === 'Custom'" placeholder="Key name" :value="paramKey" class="col-4" />
    <b-form-input placeholder="Param Value" :value="paramValue" class="col-8" />
  </b-input-group>
</template>

<script>
export default {
  props: {
    paramKeys: {
      type: Array,
      default: () => []
    },
    paramKey: {
      type: String,
      default: ''
    },
    paramValue: {
      type: String,
      default: ''
    }
  }
}
</script>

<style>

</style>
