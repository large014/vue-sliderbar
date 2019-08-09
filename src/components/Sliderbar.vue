<template>
  <div class="sliderbar_wrap">
    <div ref="sliderbar_inner" class="sliderbar_inner">
      <div ref="activebar" class="activebar" :style="activebar_styles"></div>
      <div ref="range_min_bar" class="range_min_bar range_deactivebar" :style="bar1_styles"></div>
      <div ref="range_max_bar" class="range_max_bar range_deactivebar" :class="{off : settings.type!= 'range' }" :style="bar2_styles"></div>
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
    <SliderScale :color="this.settings.scale_C" :wrap_top="this.settings.scale_BaseTop" :scale_top="this.settings.scale_Top" :step="this.settings.scale_Step"/>
  </div>
</template>

<script>

import SliderScale from './SliderScale.vue'

export default {
  name: 'sliderbar',
  components: {
    SliderScale
  },
  props: {
    msg: String,
    // type : String,
    // rate : {
    //             type:Number,
    //             default: 100
    //         },
    settings: {
        type : Object,
        default:null
    }
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
      rateValue1 : 0,
      rateValue2 : 0,
      limitX : 0,
      handleW : 0,
      barW : 0,
      min_barW : 0,
      max_barW : 0,
      stepValue_list : [],
      stepPos_list : [],
      current_step : 0
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
      let range = (this.settings.type == "range") ? true : false;
      console.log('range = ' + range);
      return range
    },
    activebar_styles(){
      return{
        width:this.barW +'px',
        '--bar_h' : this.settings.bar_H
      }
    },
    bar1_styles(){
      return{
        width:this.min_barW +'px',
        '--activebar_c' : this.settings.activebar_C,
        '--deactivebar_c' : this.settings.deactivebar_C,
        '--bar_h' : this.settings.bar_H,
        '--gageArea_c' : this.settings.gageArea_C
      }
    },
    bar2_styles(){
      return{
        left:this.max_barW +'px',
        '--bar_h' : this.settings.bar_H
      }
    },
  },

  mounted(){

    console.log('min_value = ' + this.settings.min_value);
    console.log('xx = ' + (this.settings.max_value / this.$sliderbar_inner.clientWidth) );
    console.log('長さ = ' + this.$sliderbar_inner.clientWidth);
    console.log('ハンドル = ' + this.$handle1_wrap.clientWidth);

    //--- イベント設定
    window.addEventListener("mousemove", this.touchmove.bind(this));
    window.addEventListener("mouseup", this.touchend.bind(this));

    //--- 初期値の設定
    this.limitX = this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth;
    this.handleW = this.$handle1_wrap.clientWidth;
    if(this.settings.type != "range"){
      this.handleW = 0 //--- handleの幅
    } else {
      this.barW = this.$sliderbar_inner.clientWidth; //--- アクティブバーの長さの設定
    }
    //--- rangeタイプの場合のmaxバーの設定
    this.x2 = (this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth);
    this.moveX2 = this.x2;
    this.max_barW = this.moveX2;
    // console.log(this.settings);
    // console.log(this.settings.activebar_C);
    console.log(' h = ' + ( -(this.settings.min_value) + this.settings.max_value ));

    //--- stepありの場合の設定
    for (let index = 1; index < (this.settings.scale_Step + 2); index++) {
      this.stepValue_list.push( (  ( ( -(this.settings.min_value) + this.settings.max_value ) / (this.settings.scale_Step + 1) ) * index ) + this.settings.min_value );
    }

    for (let index = 0; index < (this.settings.scale_Step + 2); index++) {
      this.stepPos_list.push( ( this.$sliderbar_inner.clientWidth / (this.settings.scale_Step + 1) ) * index );
    }

    console.log(this.stepValue_list);
    console.log(this.stepPos_list);
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
          this.moveX1 = this.setMoveValue(this.moveX1, e, this.handle1_pos.x);
          console.log('▲ moveX1 = ' + this.moveX1 + ", △ moveX2 = " + this.moveX2 + "▲ value1 = " + this.value1);
          this.handle1_setPosition();

          console.log('▲ rateValue1 = ' + this.rateValue1);
          // console.log('a = ' + this.testA + ', moveX1 = ' + this.moveX1);
        } else {
          this.handle2_pos.movedX = e.offsetX - this.handle2_pos.x;
          this.handle2_pos.movedY = e.offsetY - this.handle2_pos.y;
          this.moveX2 = this.setMoveValue(this.moveX2, e, this.handle2_pos.x);
          console.log('▲ moveX1 = ' + this.moveX1 + ", △ moveX2 = " + this.moveX2 + "▲ value2 = " + this.value2);
          this.handle2_setPosition();

          console.log('△ rateValue2 = ' + this.rateValue2);
        }
        this.setActiveBar();
        // if(this.settings.type !="range"){
        //   this.barW = this.moveX1;
        // }
      }
    },
    touchend(){
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
        this.rateValue1 = 0;
      } else if( this.moveX1 > (this.moveX2 - this.handleW) ){
        this.moveX1 = this.moveX2 - this.handleW;
        this.x1 = this.moveX2 - this.handleW;
      } else if( this.moveX1 > (this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth) ){
        this.x1 = this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth;
      } else {
        this.x1 = this.moveX1;
      }
      this.value1 = Math.floor(this.moveX1 *  ( ( (this.settings.max_value - this.settings.min_value) / (this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth) ) ) + this.settings.min_value );
      this.rateValue1 = Math.round( this.moveX1 * ( this.settings.rate / this.limitX) );

      //---- step時の処理
      if(this.settings.scale_Step != 0){
        console.log('stepあり');
        for (let index = 0; index < this.stepPos_list.length; index++) {
          if( this.moveX1 < this.stepPos_list[index] ){
            if( this.moveX1 < ( this.stepPos_list[index - 1] + this.stepPos_list[index] ) / 2 ){
              this.x1 = this.stepPos_list[index - 1];
            } else {
              this.x1 = this.stepPos_list[index];
              if( this.x1 > (this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth) ){
                this.x1 = this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth;
              }
            }
            break;
          }
        }
      }

      if(this.settings.type=="range"){
        this.min_barW = this.moveX1;
      }
    },
    handle2_setPosition(){
      if( this.moveX2 < 0){
        this.x2 = 0
        this.rateValue2 = 0;
      } else if( this.moveX2 < (this.moveX1 + this.handleW) ){
        this.moveX2 = this.moveX1 + this.handleW;
        this.x2 = this.moveX1 + this.handleW;
      } else if( this.moveX2 > (this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth) ){
        this.x2 = this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth;
      } else {
        this.x2 = this.moveX2;
      }

      this.value2 = Math.floor(this.moveX2 *  ( ( (this.settings.max_value - this.settings.min_value) / (this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth) ) ) + this.settings.min_value );
      this.rateValue2 = Math.round( this.moveX2 * ( this.settings.rate / this.limitX) );

      // //---- step時の処理
      // this.setStepHandlePos(this.moveX2, this.$handle2_wrap, this.x2 );
      if(this.settings.scale_Step != 0){
        console.log('stepあり');
        for (let index = 0; index < this.stepPos_list.length; index++) {
          if( this.moveX2 < this.stepPos_list[index] ){
            if( this.moveX2 < ( this.stepPos_list[index - 1] + this.stepPos_list[index] ) / 2 ){
              this.x2 = this.stepPos_list[index - 1];
            } else {
              this.x2 = this.stepPos_list[index];
              if( this.x2 > (this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth) ){
                this.x2 = this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth;
              }
            }
            break;
          }
        }
      }

      if(this.settings.type=="range"){
        this.max_barW = this.moveX2;
      }
    },
    setActiveBar(){
        if(this.settings.type !="range"){
          // this.barW = this.moveX1;
          this.barW = this.x1;
        }
    },
    setStepHandlePos(_moveVal, _handle, _tgtX){
      //---- step時の処理
      if(this.settings.scale_Step != 0){
        console.log('stepあり = ' + _moveVal + "_tgtX = " + _tgtX);
        for (let index = 0; index < this.stepPos_list.length; index++) {
          if( _moveVal < this.stepPos_list[index] ){
            if( _moveVal < ( this.stepPos_list[index - 1] + this.stepPos_list[index] ) / 2 ){
              console.log('hit1');
              _tgtX = this.stepPos_list[index - 1];
            } else {
              console.log('hit2');
              _tgtX = this.stepPos_list[index];
              if( _tgtX > (this.$sliderbar_inner.clientWidth - _handle.clientWidth) ){
                _tgtX = this.$sliderbar_inner.clientWidth - _handle.clientWidth;
              }
            }
            break;
          }
        }
      }
    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.sliderbar_wrap{
  --activebar_c : #05d0ff;
  --deactivebar_c : #CCC;
  --bar_h : 20px;
  --gageArea_c : #000;
  position: relative;
  // --deactivebar_c2 : #CCC;
}
.sliderbar_inner{
  position: relative;
  cursor: pointer;
  overflow: hidden;
}
.activebar{
  position: absolute;
  top: 50%;
  transform: translate(0, -50%);
  width: 100%;
  // height: 20px;
  height: var(--bar_h);
  background-color: var(--activebar_c);

}
.gageArea{
  width: 100%;
  height: 20px;
  background-color: var(--gageArea_c);
}

.range_deactivebar{
  position: absolute;
  top: 50%;
  transform: translate(0, -50%);
  width: 100%;
  // height: 20px;
  height: var(--bar_h);
  background-color: var(--deactivebar_c);
}

.range_min_bar{
  // right: 0;
  // background-color: var(--deactivebar_c);

}

.range_max_bar{
  right: 0;
  &.off{
    display: none;
  }
  // background-color: var(--deactivebar_c);
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
