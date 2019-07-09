<template>
  <div class="sliderbar_wrap">
    <div ref="sliderbar_inner" class="sliderbar_inner">
      <div ref="handle1_wrap" class="handle1_wrap" @mousedown="touchstart" @mouseup="touchend" :style="{left:x1 + 'px'}">
        <slot name="handle1"></slot>
      </div>
      <div ref="handle2_wrap" class="handle2_wrap" @mousedown="touchstart" @mouseup="touchend" :style="{left:x2 + 'px'}">
        <slot name="handle2"></slot>
      </div>
      <div class="gageArea">
        <div class="gage"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'sliderbar',
  props: {
    msg: String
  },
  data(){
    return{
      isMouseDown : false,
      handle1_pos : { x:0, y:0, movedX:0, movedY:0, addX:0 },
      handle2_pos : { x:0, y:0, movedX:0, movedY:0 },
      x1:0,
      x2:0,
      handleType : 0,
      moveX:0,
      value : 0
    }
  },
  computed:{
    $handle1_wrap() {
      return this.$refs.handle1_wrap;
    },
    $handle2_wrap() {
      return this.$refs.handle2_wrap;
    },
    _x1(){
      console.log('xx = ' + '${this.x1}px');
      return '${this.x1}px';
    },
    _x2(){
      return '${this.x2}px';
    },
    $sliderbar_inner(){
      return this.$refs.sliderbar_inner;
    }
  },
  mounted(){
    window.addEventListener("mousemove", this.touchmove.bind(this));
    window.addEventListener("mouseup", this.touchend.bind(this));
  },
  methods:{
    touchstart(e){
      this.isMouseDown = true;
      if(e.target.className =="handle1_wrap"){
        // this.handle1_pos.x = e.offsetX;
        // this.handle1_pos.y = e.offsetY;
        this.handle1_pos.x = e.clientX - parseInt(this.$handle1_wrap.style.left.replace("px",""));
        // this.handle1_pos.y = e.offsetY;
        this.handleType = 0;
      }
    },
    touchmove(e){
      if(this.isMouseDown){
        if(this.handleType == 0){
          this.handle1_pos.movedX = e.offsetX - this.handle1_pos.x;
          this.handle1_pos.movedY = e.offsetY - this.handle1_pos.y;
          // this.$handle1_wrap = e.offsetX;
          // this.handle1_pos.addX += this.handle1_pos.movedX;
          // this.x1 += this.handle1_pos.movedX
          // this.moveX = this.handle1_pos.addX + "px";
          // this.handle1_pos.x = e.offsetX;
          // this.handle1_pos.y = e.offsetY;
          this.moveX = Math.round(e.clientX - this.handle1_pos.x);
          // console.log('moveX = ' + this.moveX);
          // console.log('w = ' + this.$handle1_wrap.clientWidth);
          this.value = Math.round( this.moveX * ( 100 / 290) );
          this.setPosition();
          console.log('value = ' + this.value);
          // console.log('this.x1 = ' + this.x1);
          // this.value = Math.round(left - rect.left - width);
        }
      }
      // console.log("m");
    },
    touchend(){
      console.log("e");
      this.isMouseDown = false;
      this.setPosition();
    },
    setPosition(){
      if( this.moveX < 0){
        this.x1 = 0
        this.value = 0;
      } else if( this.moveX > (this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth) ){
        console.log('case2');
        this.x1 = this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth;
        this.value = 290;
      } else {
        this.x1 = this.moveX;
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.sliderbar_inner{
  position: relative;
  cursor: pointer;
}
.gageArea{
  width: 100%;
  height: 20px;
  background-color: #CCC;
}

.handle1_wrap{
  cursor: pointer;
  position: absolute;
  top: 0;
}

.handle2_wrap{
  cursor: pointer;
  position: absolute;
  top: 0;
  right: 0;
}

.handle{
  pointer-events: none;
}
</style>
