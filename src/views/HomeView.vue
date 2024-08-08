<template>
  <div class="home" style="text-align: center; padding-top: 2.5rem;">
    <img alt="Vue logo" src="../assets/logo.png">
    <HelloWorld msg="Dientito feliz, tu consultorio dental! :D"/>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'HomeView',
  components: {
    HelloWorld
  }
}
</script>
