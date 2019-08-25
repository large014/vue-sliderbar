<template>
  <div id="app">
    <div class="sliderbarArea">
      <!-- <Sliderbar ref="sliderbar" class="sliderbar" :settings="settings">
        <div slot="handle1" class="handle handle1">
        </div>
        <div slot="handle2" class="handle handle2">
        </div>
      </Sliderbar>
      <button @click="getValue1">value1 取得</button>
      <button @click="getValue2">value2 取得</button> -->
      <div class="sample sample1">
        <Sample1/>
      </div>
      <div class="sample sample2">
        <Sample2/>
      </div>
      <div class="sample sample3">
        <Sample3/>
      </div>
      <div class="sample sample4">
        <Sample4/>
      </div>
      <div class="sample sample5">
        <Sample5/>
      </div>
    </div>
  </div>
</template>

<script>
// import Sliderbar from './components/slideBar/Sliderbar.vue'
import Sample1 from './components/Sample1.vue'
import Sample2 from './components/Sample2.vue'
import Sample3 from './components/Sample3.vue'
import Sample4 from './components/Sample4.vue'
import Sample5 from './components/Sample5.vue'

export default {
  name: 'app',
  components: {
    // Sliderbar,
    Sample1,
    Sample2,
    Sample3,
    Sample4,
    Sample5,
  },
  data(){
    return{
      settings:{
        type : 'normal',
        width : '300px',
        activebar_C : "#07cdAA",
        deactivebar_C : "#CCC",
        gageArea_C : "#CCC",
        gageArea_H : "30px",
        gageArea_R : "30px",
        bar_H : "12px",
        bar_R : "3px",
        // handle_W : "20px",
        // handle_H : "20px",
        handle_R : "15px",
        handle_min_C : "#3f51b5",
        handle_max_C : "#ff51b5",
        min_value : 500,
        max_value : 0,
        // init_value1 : 200,
        init_value1 : "水",
        // init_value2 : "金",
        rate: 100,
        // tick_ValueList:[0, 100, 200, 300, 400, 500],
        // tick_ValueList:["月","火","水","木","金","土","日"],
        // isscale : true,
        // scale_Step : 3,
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

//---- Debug
.sliderbar{
  margin: 0 auto;
}

.sample{
  width: 400px;
  margin: 0 auto;
  margin-bottom: 40px;
}

.sliderbarArea{
  margin-top: 50px;
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
