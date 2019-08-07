<template>
  <div ref="sliderscale_wrap" class="sliderscale_wrap" :style="scaleStyles">
    <div class="scalebar start"></div>
    <div class="scalebar end"></div>
    <div v-for="n in step" :key= "n" class="scalebar" :ref= n ></div>
  </div>
</template>

<script>
export default {
  name: 'SliderScale',
  props: {
    step:Number,
    color : {},
    wrap_top:{},
    scale_top:{},
  },
  computed:{
    $sliderscale_wrap(){
      console.log('computed');
      return this.$refs.sliderscale_wrap;
    },
    scaleStyles(){
      return{
        '--scale_c' : this.color,
        '--wrap_top' : this.wrap_top,
        '--scale_top' : this.scale_top
      }
    }
  },
  mounted(){
    console.log('scale w = ' + this.$sliderscale_wrap.clientWidth);
    console.log(this.$refs);
    console.log('refs = ' + this.$refs.length );
    let cnt = 0;
    for (const key in this.$refs) {
       console.log("--" + key, this.$refs[key]);
       if(key != "sliderscale_wrap"){
         cnt +=1;
         this.$refs[key][0].style.left = ( this.$sliderscale_wrap.clientWidth / (this.step + 1) ) * cnt + "px";
       }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.sliderscale_wrap{
  --scale_c : "#000";
  --wrap_top : "-10px";
  --scale_top : "10px";
  position: absolute;
  width: 100%;
  height: 2px;
  top: var(--wrap_top);
  background-color: var(--scale_c);
}
.scalebar{
  height: 20px;
  position: absolute;
  width: 2px;
  // background-color: var(--scale_c);
  border-left: 1px solid var(--scale_c);
  top: var(--scale_top);

  &.start{
    left :0;
  }
  &.end{
    right: -1px;
  }
}
</style>
