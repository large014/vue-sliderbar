<template>
  <div class="sliderbar_wrap">
    <div ref="sliderbar_inner" class="sliderbar_inner">
      <div ref="activebar" class="activebar" :style="{width:barW +'px'}"></div>
      <div ref="handle1_wrap" class="handle1_wrap" @mousedown="touchstart" @mouseup="touchend" :style="{left:x1 + 'px'}" data_id="handle1_wrap">
        <slot name="handle1"></slot>
      </div>
      <div ref="handle2_wrap" class="handle2_wrap" :class="{on:getHandleType}" @mousedown="touchstart" @mouseup="touchend" :style="{left:x2 + 'px'}" data_id="handle2_wrap">
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
    msg: String,
    type : String,
    rate : {
                  type:Number,
                  default: 100
                },
  },
  data(){
    return{
      isMouseDown : false,
      handle1_pos : { x:0, y:0, movedX:0, movedY:0 },
      handle2_pos : { x:0, y:0, movedX:0, movedY:0 },
      x1:0,
      x2:0,
      handleType : 0,
      moveX1:0,
      moveX2:0,
      value1 : 0,
      value2 : 0,
      limitX : 0,
      handleW : 0,
      barW : 0
    }
  },
  computed:{
    $handle1_wrap() {
      return this.$refs.handle1_wrap;
    },
    $handle2_wrap() {
      return this.$refs.handle2_wrap;
    },
    $sliderbar_inner(){
      return this.$refs.sliderbar_inner;
    },
    getHandleType(){
      let range = (this.type == "range") ? true : false;
      console.log('range = ' + range);
      return range
    }
    // _x1(){
    //   console.log('xx = ' + '${this.x1}px');
    //   return '${this.x1}px';
    // },
    // _x2(){
    //   return '${this.x2}px';
    // },
  },

  mounted(){
    //--- イベント設定
    window.addEventListener("mousemove", this.touchmove.bind(this));
    window.addEventListener("mouseup", this.touchend.bind(this));

    //--- 初期値の設定
    this.limitX = this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth;
    this.handleW = this.$handle1_wrap.clientWidth;
    if(this.type != "range"){
      this.handleW = 0 //--- handleの幅
    } else {
      this.barW = this.$sliderbar_inner.clientWidth; //--- アクティブバーの長さの設定
    }


    //--- rangeタイプの場合のmaxバーの設定
    this.x2 = (this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth);
    this.moveX2 = this.x2;


  },

  methods:{
    touchstart(e){
      this.isMouseDown = true;
      switch(e.target.attributes.data_id.value){
        case "handle1_wrap" :
                              this.handle1_pos.x = e.clientX - parseInt(this.$handle1_wrap.style.left.replace("px",""));
                              this.handleType = 0;
                              break;
        case "handle2_wrap" :
                              this.handle2_pos.x = e.clientX - parseInt(this.$handle2_wrap.style.left.replace("px",""));
                              this.handleType = 1;
                              break;
      }
    },
    touchmove(e){
      if(this.isMouseDown){
        if(this.handleType == 0){
          this.handle1_pos.movedX = e.offsetX - this.handle1_pos.x;
          this.handle1_pos.movedY = e.offsetY - this.handle1_pos.y;
          // this.moveX1 = Math.round(e.clientX - this.handle1_pos.x);
          this.moveX1 = this.setMoveValue(this.moveX1, e, this.handle1_pos.x);
          console.log('▲ moveX1 = ' + this.moveX1 + ", △ moveX2 = " + this.moveX2);
          // this.value1 = Math.round( this.moveX1 * ( 100 / this.limitX) );
          this.handle1_setPosition();

          // console.log('a = ' + this.testA + ', moveX1 = ' + this.moveX1);
          console.log('▲ value1 = ' + this.value1);
        } else {
          this.handle2_pos.movedX = e.offsetX - this.handle2_pos.x;
          this.handle2_pos.movedY = e.offsetY - this.handle2_pos.y;
          // this.moveX2 = Math.round(e.clientX - this.handle2_pos.x);
          this.moveX2 = this.setMoveValue(this.moveX2, e, this.handle2_pos.x);
          // this.value2 = Math.round( this.moveX2 * ( 100 / this.limitX) );
          this.handle2_setPosition();

          console.log('△ value2 = ' + this.value2);
          // console.log('△ moveX2 = ' + this.moveX2 + ", ▲ moveX1 = " + this.moveX1);
        }
        this.barW = this.moveX1;
      }
      // console.log("m");
    },
    touchend(){
      console.log("e");
      this.isMouseDown = false;
      console.log('END | moveX1 = ' + this.moveX1 + ", moveX2 = " + this.moveX2 );
    },
    setMoveValue( _val, e, posX ){
      _val = Math.round(e.clientX - posX );
      if( _val > this.limitX){
        _val = this.limitX;
      }
      if(_val < 0){
        _val = 0;
      }
      return _val;
    },
    handle1_setPosition(){
      if( this.moveX1 < 0){
        this.x1 = 0
        this.value1 = 0;
      } else if( this.moveX1 > (this.moveX2 - this.handleW) ){
        this.moveX1 = this.moveX2 - this.handleW;
        this.x1 = this.moveX2 - this.handleW;
        // console.log('1. case2 | moveX1 = ' + this.moveX1 + ", moveX2 = " + this.moveX2 );
      } else if( this.moveX1 > (this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth) ){
        this.x1 = this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth;
        // this.value1 = this.maxvalue;
        // console.log('1. case3');
      } else {
        this.x1 = this.moveX1;
        // console.log('1. case4')
      }
      this.value1 = Math.round( this.moveX1 * ( this.rate / this.limitX) );
    },
    handle2_setPosition(){
      if( this.moveX2 < 0){
        this.x2 = 0
        this.value2 = 0;
      } else if( this.moveX2 < (this.moveX1 + this.handleW) ){
        this.moveX2 = this.moveX1 + this.handleW;
        this.x2 = this.moveX1 + this.handleW;
        // console.log('2. case2 | moveX1 = ' + this.moveX1 + ", moveX2 = " + this.moveX2);
      } else if( this.moveX2 > (this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth) ){
        this.x2 = this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth;
        // this.value2 = this.maxvalue;
        // console.log('2. case3');
      } else {
        this.x2 = this.moveX2;
        // console.log('2. case4 = ' + this.moveX2);
      }
      this.value2 = Math.round( this.moveX2 * ( this.rate / this.limitX) );
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.sliderbar_inner{
  position: relative;
  cursor: pointer;
}
.activebar{
  position: absolute;
  width: 100%;
  height: 20px;
  background-color: #07cdAA;
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
  opacity: 0;
  pointer-events: none;
  &.on{
    opacity: 1;
    pointer-events: auto;
  }
}

.handle{
  pointer-events: none;
}
</style>
