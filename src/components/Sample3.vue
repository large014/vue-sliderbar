<template>
  <div class="sample3_wrappear">
      <div class="sliderbarArea">
        <p class="text">例3 : 範囲-100 〜 400 ハンドルに画像設定</p>
        <Sliderbar ref="sliderbar" class="sliderbar" :settings="settings" @bar_update="getValue">
          <div slot="handle1" class="handle handle1">
            <img src="@/assets/handle.png" alt="">
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
  name: 'Sample3',
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
        bar_H : "20px",
        handle_W : "20px",
        handle_H : "20px",
        handle_max_C : "#ff51b5",
        min_value : -100,
        max_value : 400,
        rate: 100,
        isscale : true,
        scale_Step : 3,
        scale_C : "#000",
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
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.text{
  font-weight: bold;
  margin-bottom: 40px;
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
