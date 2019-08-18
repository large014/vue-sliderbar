<template>
  <div id="app">
    <div class="sliderbarArea">
      <Sliderbar ref="sliderbar" class="sliderbar" :settings="settings">
        <div slot="handle1" class="handle handle1">
        </div>
        <div slot="handle2" class="handle handle2">
        </div>
      </Sliderbar>
      <button @click="getValue1">value1 取得</button>
      <button @click="getValue2">value2 取得</button>
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
        type : 'range',
        activebar_C : "#07cdAA",
        deactivebar_C : "#CCC",
        gageArea_C : "#CCC",
        bar_H : "12px",
        bar_W : "10px",
        min_value : 0,
        max_value : 500,
        // init_value1 : 200,
        // init_value2 : "金",
        rate: 100,
        // tick_ValueList:[0, 100, 200, 300, 400, 500],
        // tick_ValueList:["月","火","水","木","金","土","日"],
        scale_Step : 3,
        scale_C : "#F00",
        scale_BaseTop : "-10px",
        scale_Top : "-10px",
      }
    }
  },
  methods:{
    getValue1(){
      console.log('value = ' + this.$refs.sliderbar.stepValue1);
    },
    getValue2(){
      console.log('value = ' + this.$refs.sliderbar.stepValue2);
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
}
.handle1{
  background: #3f51b5;
}
.handle2{
  background: #ff51b5;
}

button{
  cursor: pointer;
  border: 1px solid #000;
  background-color: #CCC;
  padding: 10px;
  margin: 4px;
  margin-top: 20px;
}
</style>
