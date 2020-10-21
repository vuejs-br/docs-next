<template>
  <SponsorsLayout extra-classes="sponsors__open-collective">
    <template #heading>Patrocínio no Open Collective</template>

    <template #content>
      <h4>Platina</h4>
      <a
        :href="`https://opencollective.com/vuejs/tiers/platinum-sponsors/${index}/website`"
        rel="sponsored noopener"
        target="_blank"
        v-for="(_, index) in new Array(featuredCount.platinum)"
      >
        <img
          :src="`https://opencollective.com/vuejs/tiers/platinum-sponsors/${index}/avatar.svg`"
          alt="Patrocinador Platina do Vue.JS"
        >
      </a>

      <h4>Ouro</h4>
      <a
        :href="`https://opencollective.com/vuejs/tiers/gold-sponsors/${index}/website`"
        rel="sponsored noopener"
        target="_blank"
        v-for="(_, index) in new Array(featuredCount.gold)"
      >
        <img
          :src="`https://opencollective.com/vuejs/tiers/gold-sponsors/${index}/avatar.svg`"
          alt="Patrocinador Ouro do Vue.JS"
        >
      </a>
    </template>
  </SponsorsLayout>
</template>

<script>
import SponsorsLayout from '@theme/components/sponsors/SponsorsLayout.vue'

export default {
  components: {
    SponsorsLayout
  },

  data: () => ({
    featuredCount: {
      platium: 3,
      gold: 10
    }
  })
}
</script>

<style lang="scss" scoped>
img {
  width: auto;
  max-width: 100px !important;
  max-height: 60px !important;
}
</style>
