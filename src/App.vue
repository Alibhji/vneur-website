<template>
  <div class="app-container">
    <ThreeJSText />
    <EmailLink />
  </div>
</template>

<script>
import ThreeJSText from './components/ThreeJSText.vue'
import EmailLink from './components/EmailLink.vue'

export default {
  name: 'App',
  components: {
    ThreeJSText,
    EmailLink
  }
}
</script>

<style scoped>
.app-container {
  width: 100vw;
  height: 100vh;
  position: relative;
  overflow: hidden;
}
</style>

