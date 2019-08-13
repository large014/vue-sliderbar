<template>
  <div class="sliderbar_wrap">
    <div ref="sliderbar_inner" class="sliderbar_inner">
      <div ref="activebar" class="activebar" :style="activebar_styles"></div>
      <div ref="range_min_bar" class="range_min_bar range_deactivebar" :style="bar1_styles"></div>
      <div ref="range_max_bar" class="range_max_bar range_deactivebar" :class="{off : settings.type!= 'range' }" :style="bar2_styles"></div>
      <div ref="handle1_wrap" class="handle1_wrap" @mousedown="touchstart" @mouseup="touchend" :style="handle1_styles" data_id="handle1_wrap">
        <slot name="handle1"></slot>
      </div>
      <div ref="handle2_wrap" class="handle2_wrap" :class="{on:getHandleType}" @mousedown="touchstart" @mouseup="touchend" :style="handle2_styles" data_id="handle2_wrap">
        <slot name="handle2"></slot>
      </div>
      <div class="gageArea">
        <div class="gage"></div>
      </div>
    </div>
    <SliderScale :color="this.settings.scale_C" :wrap_top="this.settings.scale_BaseTop" :scale_top="this.settings.scale_Top" :step="this.settings.scale_Step" :barW="this.settings.bar_W"/>
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
      stepValue1 : 0,
      stepValue2 : 0,
      limitX : 0,
      handleW : 0,
      barW : 0,
      min_barW : 0,
      max_barW : 0,
      stepValue_list : [],
      stepPos_list : [],
      handle1_z : 1,
      handle2_z : 1,
      step : 0
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
      return range
    },
    activebar_styles(){
      return{
        width:this.barW +'px',
        '--bar_h' : this.settings.bar_H
      }
    },
    handle1_styles(){
      return{
        left:this.x1 + 'px',
        zIndex : this.handle1_z,
        '--bar_w' : this.settings.bar_W
      }
    },
    handle2_styles(){
      return{
        left:this.x2 + 'px',
        zIndex : this.handle2_z,
        '--bar_w' : this.settings.bar_W
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

    // console.log('min_value = ' + this.settings.min_value);
    // console.log('xx = ' + (this.settings.max_value / this.$sliderbar_inner.clientWidth) );
    // console.log('長さ = ' + this.$sliderbar_inner.clientWidth);
    // console.log('ハンドル = ' + this.$handle1_wrap.clientWidth);

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


    //--- scale_Stepとscale_Dataの整合性
    if( this.settings.scale_Data === void 0 ){
      console.log('ないよー');
      this.step = this.settings.scale_Step;
      //--- scale_Stepありの場合のValueの設定
      for (let index = 0; index < (this.step + 2); index++) {
        this.stepValue_list.push( (  ( ( -(this.settings.min_value) + this.settings.max_value ) / (this.step + 1) ) * index ) + this.settings.min_value );
      }
    } else {
      console.log('あるよー');
      //--- scale_Dataありの場合のValue設定
      this.stepValue_list = this.settings.scale_Data;
      this.step = this.settings.scale_Data.length - 2;
    }

    //--- stepありの場合の目盛り判定ポジションの設定
    for (let index = 0; index < (this.step + 2); index++) {
      if( index != 0 && index != (this.step + 1) ) {
        this.stepPos_list.push( ( ( this.$sliderbar_inner.clientWidth / (this.step + 1) ) * index ) - (this.$handle1_wrap.clientWidth / 2) );
      } else {
        this.stepPos_list.push( ( this.$sliderbar_inner.clientWidth / (this.step + 1) ) * index );
      }
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
          //---- ハンドルのz-index設定
          this.handle1_z = 2;
          this.handle2_z = 1;
          this.moveX1 = this.setMoveValue(this.moveX1, e, this.handle1_pos.x);
          console.log('▲ moveX1 = ' + this.moveX1 + ", △ moveX2 = " + this.moveX2 + "▲ value1 = " + this.value1);
          this.handle1_setPosition();

          console.log('▲ rateValue1 = ' + this.rateValue1 + ', ▲ stepValue1 = ' + this.stepValue1);
        } else {
          //---- ハンドルのz-index設定
          this.handle1_z = 1;
          this.handle2_z = 2;
          this.moveX2 = this.setMoveValue(this.moveX2, e, this.handle2_pos.x);
          console.log('▲ moveX1 = ' + this.moveX1 + ", △ moveX2 = " + this.moveX2 + "▲ value2 = " + this.value2);
          this.handle2_setPosition();

          console.log('△ rateValue2 = ' + this.rateValue2 + ', △ stepValue2 = ' + this.stepValue2);
        }
        //---- アクティブバーの設定
        if(this.settings.type !="range"){
          this.barW = this.x1;
        }
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

      //--- %の取得
      // this.rateValue1 = Math.round( this.moveX1 * ( this.settings.rate / this.limitX) );
      this.rateValue1 =  this.getRateValue(this.moveX1);

      //--- range時のバーの設定
      if(this.settings.type=="range"){
        this.min_barW = this.moveX1;
      }

      //---- step時の処理
      this.setStepHandlePos(this.moveX1, this.$handle1_wrap, this.stepValue1);
      // if(this.step != 0){
      //   console.log('stepあり');
      //   for (let index = 0; index < this.stepPos_list.length; index++) {
      //     if( this.moveX1 < this.stepPos_list[index] ){
      //       if( this.moveX1 < ( this.stepPos_list[index - 1] + this.stepPos_list[index] ) / 2 ){
      //         this.x1 = this.stepPos_list[index - 1];
      //         this.stepValue1 = this.stepValue_list[index - 1];
      //       } else {
      //         this.x1 = this.stepPos_list[index];
      //         this.stepValue1 = this.stepValue_list[index];
      //         if( this.x1 > (this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth) ){
      //           this.x1 = this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth;
      //         }
      //       }
      //       break;
      //     }
      //   }
      //   //--- range時のバーの設定
      //   if(this.settings.type=="range"){
      //     this.min_barW = this.x1;
      //   }
      // }
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
      //--- %の取得
      // this.rateValue2 = Math.round( this.moveX2 * ( this.settings.rate / this.limitX) );
      this.rateValue2 =  this.getRateValue(this.moveX2);

      //--- range時のバーの設定
      if(this.settings.type=="range"){
        this.max_barW = this.moveX2;
      }

      //---- step時の処理
      this.setStepHandlePos(this.moveX2, this.$handle2_wrap, this.stepValue2);
      // if(this.step != 0){
      //   console.log('stepあり');
      //   for (let index = 0; index < this.stepPos_list.length; index++) {
      //     if( this.moveX2 < this.stepPos_list[index] ){
      //       if( this.moveX2 < ( this.stepPos_list[index - 1] + this.stepPos_list[index] ) / 2 ){
      //         this.x2 = this.stepPos_list[index - 1];
      //         this.stepValue2 = this.stepValue_list[index - 1];
      //       } else {
      //         this.x2 = this.stepPos_list[ index ];
      //         this.stepValue2 = this.stepValue_list[ index ];
      //         if( this.x2 > (this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth) ){
      //           this.x2 = this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth;
      //         }
      //       }
      //       break;
      //     }
      //   }
      //   //--- range時のバーの設定
      //   if(this.settings.type=="range"){
      //     this.max_barW = this.x2;
      //   }
      // }
    },
    setStepHandlePos(_moveVal, _handle){
      //---- step時の処理
      if(this.step != 0){
        console.log('stepあり = ' + _moveVal);
        for (let index = 0; index < this.stepPos_list.length; index++) {
          if( _moveVal < this.stepPos_list[index] ){
            if( _moveVal < ( this.stepPos_list[index - 1] + this.stepPos_list[index] ) / 2 ){
              if( this.handleType == 0){
                this.x1 = this.stepPos_list[index - 1];
                this.stepValue1 = this.stepValue_list[index - 1];
                this.rateValue1 =  this.getRateValue(this.x1 + (this.$handle1_wrap.clientWidth / 2) );
              } else {
                this.x2 = this.stepPos_list[index - 1];
                this.stepValue2 = this.stepValue_list[index - 1];
                this.rateValue2 =  this.getRateValue(this.x2 + (this.$handle2_wrap.clientWidth / 2));
              }
            } else {
              if( this.handleType == 0){
                this.x1 = this.stepPos_list[index];
                this.stepValue1 = this.stepValue_list[ index ];
                this.rateValue1 =  this.getRateValue(this.x1 + (this.$handle1_wrap.clientWidth / 2));
                if( this.x1 > (this.$sliderbar_inner.clientWidth - _handle.clientWidth) ){
                  this.x1 = this.$sliderbar_inner.clientWidth - _handle.clientWidth;
                }
              } else {
                this.x2 = this.stepPos_list[index];
                this.stepValue2 = this.stepValue_list[ index ];
                this.rateValue2 =  this.getRateValue(this.x2 + (this.$handle2_wrap.clientWidth / 2));
                if( this.x2 > (this.$sliderbar_inner.clientWidth - _handle.clientWidth) ){
                  this.x2 = this.$sliderbar_inner.clientWidth - _handle.clientWidth;
                }
              }
            }
            break;
          }
        }
        //--- range時のバーの設定
        if(this.settings.type=="range"){
          if(this.handleType == 0){
            this.min_barW = this.x1;
          } else {
            this.max_barW = this.x2;
          }
        }
      }
    },
    getRateValue( _moveX){
      // console.log('rate X = ' + _moveX + ", rate = " + this.settings.rate + ", limitX = " + this.limitX);
      // return Math.round( _moveX * ( this.settings.rate / this.limitX) );
      return Math.round( _moveX * ( this.settings.rate / (this.limitX + this.$handle1_wrap.clientWidth ) ) );
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
  --bar_w : 10px;
  --gageArea_c : #000;
  position: relative;
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
}

.handle1_wrap{
  position: absolute;
  top: 0;
}

.handle2_wrap{
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
  cursor: pointer;
  pointer-events: none;
  width: var(--bar_w);
}
</style>
