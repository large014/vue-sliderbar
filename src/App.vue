<template>
  <div id="app">
    <Sliderbar class="sliderbar">
      <div slot="handle1" class="handle handle1">
        <!-- <p class="handle"></p> -->
      </div>
      <div slot="handle2" class="handle handle2">
        <!-- <p class="handle"></p> -->
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
  // position: absolute;
  // top: 0;

  background: #3f51b5;
  border: none;
  border-radius: 3px;
  // cursor: pointer;
  height: 20px;
  width: 10px;
  // margin-top: -14px;
}

.handle2{
    background: #ff51b5;
}
</style>
