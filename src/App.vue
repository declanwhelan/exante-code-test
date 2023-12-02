<template>
  <IrradiationProgressCard />
</template>

<script>
import IrradiationProgressCard from 'components/IrradiationProgressCard.vue'

export default {
  name: 'App',
  components: {
    IrradiationProgressCard
  }
}
</script>

<style lang="scss">
#app {
  font-family: Calibri, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding: 10px;
}
</style>
