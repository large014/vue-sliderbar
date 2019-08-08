<template>
  <div id="app">
    <div class="sliderbarArea">
      <Sliderbar class="sliderbar" type="normal" :rate="100" :settings="settings">
        <div slot="handle1" class="handle handle1">
        </div>
        <div slot="handle2" class="handle handle2">
        </div>
      </Sliderbar>
    </div>
  </div>
</template>

<script>
import Sliderbar from './components/Sliderbar.vue'

export default {
  name: 'app',
  components: {
    Sliderbar
  },
  data(){
    return{
      settings:{
        activebar_C : "#07cdAA",
        deactivebar_C : "#CCC",
        gageArea_C : "#CCC",
        bar_H : "1px",
        min_value : 50,
        max_value : 400,
        scale_C : "#F00",
        scale_BaseTop : "-10px",
        scale_Top : "-10px",
        scale_Step : 2
      }
    }
  }
}
</script>

<style scoped lang="scss">
#app{
  text-align: center;
}
.sliderbarArea{
  margin-top: 100px;
}

.sliderbar{
  width:300px;
  margin: 0 auto;
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
