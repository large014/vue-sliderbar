<template>
  <div class="sample6_wrappear">
      <div class="sliderbarArea">
        <p class="text">例1 : 範囲0 〜 500</p>
        <Sliderbar ref="sliderbar" class="sliderbar" :settings="settings" @bar_update="getValue">
          <div slot="handle1" class="handle handle1">
          </div>
          <div slot="handle2" class="handle handle2">
          </div>
        </Sliderbar>
        <div class="valueArea">
          <p>value1 : {{value1}}</p>
          <p>rate_value1 : {{rateValue1}}%</p>
        </div>
      </div>
  </div>
</template>

<script>
import Sliderbar from './slideBar/Sliderbar.vue'

export default {
  name: 'Sample6',
  props: {
  },
  components:{
    Sliderbar
  },
  data(){
    return{
      value1 : 0,
      rateValue1 : 0,
      stepValue1 : 0,
      settings:{
        type : 'normal',
        width : '400px',
        activebar_C : "#07cdAA",
        deactivebar_C : "#CCC",
        gageArea_C : "#CCC",
        gageArea_H : "20px",
        // gageArea_R : "30px",
        bar_H : "20px",
        // bar_R : "3px",
        // handle_W : "20px",
        // handle_H : "20px",
        // handle_R : "15px",
        handle_min_C : "#3f51b5",
        handle_max_C : "#ff51b5",
        min_value : 0,
        max_value : 500,
        // init_value1 : 200,
        // init_value1 : "水",
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
  computed:{
  },
  methods:{
    getValue(){
      this.value1 = this.$refs.sliderbar.value1;
      this.rateValue1 = this.$refs.sliderbar.rateValue1;
    }
    // getValue1(){
    //   console.log('value = ' + this.$refs.sliderbar.stepValue1);
    //   this.value1 = this.$refs.sliderbar.value1;
    //   this.rateValue1 = this.$refs.sliderbar.rateValue1;
    // },
    // getValue2(){
    //   console.log('value = ' + this.$refs.sliderbar.stepValue2);
    // }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.text{
  margin-bottom: 20px;
}

.valueArea{
  width: 400px;
  border : 1px solid #000;
  padding: 10px;
  margin-top: 20px;
  p:first-child{
    margin-bottom: 5px;
  }
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
