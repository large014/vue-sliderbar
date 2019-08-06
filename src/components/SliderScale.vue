<template>
  <div class="sliderscale_wrap" :style="scaleStyles">
    <div class="scalebar start"></div>
    <div class="scalebar end"></div>
    <div v-for="n in step" :key= "n" class="scalebar"></div>
  </div>
</template>

<script>
export default {
  name: 'SliderScale',
  props: {
    step:Number,
    color : {},
    top:{},
  },
  computed:{
    scaleStyles(){
      return{
        '--scale_c' : this.color,
        '--top' : this.top
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.sliderscale_wrap{
  --scale_c : "#000";
  --top : "-10px";
  position: absolute;
  width: 100%;
  height: 1px;
  top: var(--top);
  background-color: var(--scale_c);
}
.scalebar{
  height: 20px;
  width: 2px;
  background-color: var(--scale_c);
  position: absolute;
  top: var(--top);

  &.start{
    left :0;
  }
  &.end{
    right: 0;
  }
}
</style>
