<template>
  <div id="app">
    <Sliderbar class="sliderbar" type="range" :rate="100">
      <div slot="handle1" class="handle handle1">
      </div>
      <div slot="handle2" class="handle handle2">
      </div>
    </Sliderbar>
  </div>
</template>

<script>
import Sliderbar from './components/Sliderbar.vue'

export default {
  name: 'app',
  components: {
    Sliderbar
  }
}
</script>

<style scoped lang="scss">
#app{
  text-align: center;
}
.sliderbar{
  width:300px;
  margin: 0 auto;
  padding-top: 100px;
}

.handle{
  border: none;
  border-radius: 3px;
  height: 20px;
  width: 10px;
}
.handle1{
  background: #3f51b5;
}
.handle2{
  background: #ff51b5;
}
</style>
