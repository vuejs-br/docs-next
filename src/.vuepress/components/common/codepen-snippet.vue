<template>
  <p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-preview="true" data-editable="true"
  :data-user="user.slug"
  :data-slug-hash="slug"
  :data-pen-title="title">
    <span>Veja o exemplo <a :href="`https://codepen.io/${user.slug}/pen/${slug}`">
    {{ title }}</a> por {{ user.name }} (<a :href="`https://codepen.io/${user.slug}`">@${user.slug}</a>)
    no <a href="https://codepen.io">CodePen</a>.</span>
  </p>
</template>

<script>
export default {
  props: {
    user: {
      type: Object,
      default: () => ({
        slug: 'vuejs-br',
        name: 'Vue.js Brasil'
      }),
    },

    title: {
      type: String,
      default: null,
    },

    slug: {
      type: String,
      default: null,
    },
  },
  mounted() {
    const codepenScript = document.createElement('script')
    codepenScript.setAttribute('src', 'https://static.codepen.io/assets/embed/ei.js')
    codepenScript.async = true
    this.$el.appendChild(codepenScript)
  }
}
</script>

<style lang="scss">
.codepen {
  box-sizing: border-box;
  height: 300px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 2px solid;
  margin: 1em 0;
  padding: 1em;
}
</style>
