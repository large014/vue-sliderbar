<template>
  <div class="sliderbar_wrap" :style="wrap_styles">
    <div ref="sliderbar_inner" class="sliderbar_inner" :style="gageArea_styles">
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
    <SliderScale v-if="this.settings.isscale" :color="this.settings.scale_C" :wrap_top="this.settings.scale_BaseTop" :scale_top="this.settings.scale_Top" :step="getStepNum" :barW="getHandleW"/>
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
      step : 0,
      min_tempX : 0,
      max_tempX : 1
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
    getStepNum(){
      let stepNum = 0;
      if( this.settings.tick_ValueList === void 0 ){
        if( this.settings.scale_Step != void 0){
          stepNum = this.settings.scale_Step;
        }
      } else {
        stepNum = this.settings.tick_ValueList.length - 2;
      }
      return stepNum
    },
    getHandleW(){
      let hw = 0;
      if( this.settings.handle_W === void 0){
        hw = "10px";
        // console.log('case1');
      } else {
        hw = this.settings.handle_W;
        // console.log('case2');
      }
      return hw
    },
    getHandleType(){
      let range = (this.settings.type == "range") ? true : false;
      return range
    },
    wrap_styles(){
      return{
        'width' :this.settings.width
      }
    },
    gageArea_styles(){
      return{
        '--gageArea_h' : this.settings.gageArea_H,
        '--gageArea_r' : this.settings.gageArea_R,
      }
    },
    activebar_styles(){
      return{
        width:this.barW + 0 +'px',
        '--bar_h' : this.settings.bar_H,
      }
    },
    handle1_styles(){
      return{
        left:this.x1 + 'px',
        zIndex : this.handle1_z,
        '--handle_w' : this.settings.handle_W,
        '--handle_h' : this.settings.handle_H,
        '--handle_r' : this.settings.handle_R,
        '--handle1_c' : this.settings.handle_min_C
      }
    },
    handle2_styles(){
      return{
        left:this.x2 + 'px',
        zIndex : this.handle2_z,
        '--handle_w' : this.settings.handle_W,
        '--handle_h' : this.settings.handle_H,
        '--handle_r' : this.settings.handle_R,
        '--handle2_c' : this.settings.handle_max_C
      }
    },
    bar1_styles(){
      return{
        width:this.min_barW + 1 +'px',
        '--activebar_c' : this.settings.activebar_C,
        '--deactivebar_c' : this.settings.deactivebar_C,
        '--bar_h' : this.settings.bar_H,
        '--gageArea_c' : this.settings.gageArea_C,
      }
    },
    bar2_styles(){
      return{
        left:this.max_barW  + this.max_tempX +'px',
        '--bar_h' : this.settings.bar_H
      }
    },
  },

  mounted(){
    // console.log('min_value = ' + this.settings.min_value);
    // console.log('xx = ' + (this.settings.max_value / this.$sliderbar_inner.clientWidth) );
    // console.log('長さ = ' + this.$sliderbar_inner.clientWidth);
    // console.log('ハンドル = ' + this.$handle1_wrap.clientWidth);

    //---- イベント設定
    window.addEventListener("mousemove", this.touchmove.bind(this));
    window.addEventListener("mouseup", this.touchend.bind(this));

    //---- 初期値の設定
    this.limitX = this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth;
    this.handleW = this.$handle1_wrap.clientWidth;

    if(this.settings.type != "range"){
      //---- handleの幅
      this.handleW = 0
      //---- アクティブバーにRの設定がある場合は、Leftのみ設定
      if(this.settings.gageArea_R != void 0){
        this.$refs.activebar.style.borderTopLeftRadius = this.settings.gageArea_R;
        this.$refs.activebar.style.borderBottomLeftRadius = this.settings.gageArea_R;
        this.$refs.activebar.style.marginLeft = "2px";
      }
    } else {
      //---- アクティブバーの長さの設定
      this.barW = this.$sliderbar_inner.clientWidth;

      //---- アクティブバーにRの設定がある場合は、Leftのみ設定
      if(this.settings.gageArea_R != void 0){
        this.$refs.activebar.style.borderTopLeftRadius = this.settings.gageArea_R;
        this.$refs.activebar.style.borderBottomLeftRadius = this.settings.gageArea_R;
      }

      //---- ハンドルにRの設定がある場合は、max_barの移動位置を調整
      if(this.settings.handle_R != void 0){
        this.max_tempX = 2
      }

      //---- Max値の初期値の設定
      if( this.settings.init_value2 != void 0 ){
          this.handleType = 1;
          this.setInitStepValue( 2, this.settings.init_value2 );
          this.handle2_setPosition();
      }
    }
    //---- rangeタイプの場合のmaxバーの設定
    this.x2 = (this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth);
    this.moveX2 = this.x2;
    this.max_barW = this.moveX2;

    //---- scale_Stepとtick_ValueListの整合性
    if( this.settings.tick_ValueList === void 0 ){
      this.step = this.settings.scale_Step;
      //---- scale_Stepありの場合のValueの設定
      for (let index = 0; index < (this.step + 2); index++) {
        this.stepValue_list.push( (  ( ( -(this.settings.min_value) + this.settings.max_value ) / (this.step + 1) ) * index ) + this.settings.min_value );
      }
    } else {
      //---- tick_ValueListありの場合のValue設定
      this.stepValue_list = this.settings.tick_ValueList;
      this.step = this.settings.tick_ValueList.length - 2;
    }

    //--- stepありの場合の目盛り判定ポジションの設定
    for (let index = 0; index < (this.step + 2); index++) {
      // if( index != 0 && index != (this.step + 1) ) {
        // this.stepPos_list.push( ( ( (this.$sliderbar_inner.clientWidth - 10 ) / (this.step + 1) ) * index ) );
      // } else {
        this.stepPos_list.push( ( (this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth) / (this.step + 1) ) * index );
      // }
    }

    if( this.settings.init_value1 != void 0){
      // this.moveX1 = this.settings.init_value1;
      this.setInitValue(1, this.settings.init_value1 );
      this.setInitStepValue( 1, this.settings.init_value1 );
      this.handle1_setPosition();
    }

    if( this.settings.init_value2 != void 0){
      // this.moveX1 = this.settings.init_value1;
      this.setInitValue(2, this.settings.init_value2 );
      this.setInitStepValue( 2, this.settings.init_value2 );
      this.handle2_setPosition();
    }

    // if( this.settings.type === "range" ){
    //   if( this.settings.init_value2 != void 0 ){
    //       this.handleType = 1;
    //       this.setInitStepValue( 2, this.settings.init_value2 );
    //       this.handle2_setPosition();
    //   }
    // }

    console.log(this.stepValue_list);
    console.log(this.stepPos_list);
    console.log('move2 = ' + this.moveX2);
  },

  destroyed(){
    //--- イベント削除
    window.removeEventListener("mousemove", this.touchmove.bind(this));
    window.removeEventListener("mouseup", this.touchend.bind(this));
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
          // this.moveX1 = this.setMoveValue(this.moveX1, e, this.handle1_pos.x);
          // console.log('▲ moveX1 = ' + this.moveX1 + ", △ moveX2 = " + this.moveX2 + "▲ value1 = " + this.value1);
          // this.handle1_setPosition();
          this.handle1_setPosition(e, this.handle1_pos.x);

          console.log('▲ rateValue1 = ' + this.rateValue1 + ', ▲ stepValue1 = ' + this.stepValue1 + ', ▲ value1 = ' + this.value1 + ', ▲ moveX1 = ' + this.moveX1 + ",△ moveX2 = " + this.moveX2 );
        } else {
          //---- ハンドルのz-index設定
          this.handle1_z = 1;
          this.handle2_z = 2;
          // this.moveX2 = this.setMoveValue(this.moveX2, e, this.handle2_pos.x);
          // console.log('▲ moveX1 = ' + this.moveX1 + ", △ moveX2 = " + this.moveX2 + "▲ value2 = " + this.value2);
          // this.handle2_setPosition();
          this.handle2_setPosition(e, this.handle2_pos.x);

          console.log('△ rateValue2 = ' + this.rateValue2 + ', △ stepValue2 = ' + this.stepValue2 + ', ▲ value2 = ' + this.value2 + ', ▲ moveX1 = ' + this.moveX1 + ",△ moveX2 = " + this.moveX2);
        }
        // //---- アクティブバーの設定
        // if(this.settings.type !="range"){
        //   this.barW = this.x1;
        // }
      }
    },
    touchend(){
      this.isMouseDown = false;
      console.log('END | moveX1 = ' + this.moveX1 + ", moveX2 = " + this.moveX2 );
    },
    // setMoveValue( _val, e, posX ){
    //   _val = Math.round(e.clientX - posX );
    //   if( _val > this.limitX){
    //     _val = this.limitX;
    //   }
    //   if(_val < 0){
    //     _val = 0;
    //   }
    //   return _val;
    // },
    handle1_setPosition(e, posX){
      if( e != void 0 ){
        this.moveX1 = Math.round(e.clientX - posX );
      }
      if( this.moveX1 < 0){
        this.moveX1 = 0;
        this.x1 = 0
        this.rateValue1 = 0;
      } else if( this.moveX1 > (this.moveX2 ) ){
        this.moveX1 = this.moveX2;
        this.x1 = this.moveX1;
      // } else if( this.moveX1 > (this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth) ){
      //   this.x1 = this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth;
      //   this.moveX1 = this.$sliderbar_inner.clientWidth;
      //   console.log('cccc');
      }else {
        this.x1 = this.moveX1;
      }
      // this.value1 = Math.floor(this.moveX1 *  ( ( (this.settings.max_value - this.settings.min_value) / (this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth) ) ) + this.settings.min_value );

      //--- min_value〜max_valueの範囲に対してのvalueを取得
      this.value1 = this.getValue(this.moveX1);
      //--- %の取得
      // this.rateValue1 = Math.round( this.moveX1 * ( this.settings.rate / this.limitX) );
      this.rateValue1 =  this.getRateValue(this.moveX1);

      //---- アクティブバーの設定
      // if(this.settings.type !="range"){
      //   this.barW = this.x1;
      // }

      //---- step時の処理
      this.setStepHandlePos(this.moveX1, this.$handle1_wrap);
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

      //--- アクティブバー、range時のバーの設定
      if(this.settings.type=="range"){
        // this.min_barW = this.moveX1;
        this.min_barW = this.x1;
      } else {
        //---- アクティブバーの設定
        this.barW = this.x1;
      }


    },
    handle2_setPosition(e, posX){

      if( e != void 0 ){
        this.moveX2 = Math.round(e.clientX - posX );
      }

      // if( this.moveX2 < 0){
        // this.x2 = 0
        // this.moveX2 = 0;
        // this.rateValue2 = 0;
        // console.log('ccccc');
      // } else if( this.moveX2 < (this.moveX1 + this.handleW) ){
      // } else if( this.moveX2 < (this.moveX1) ){
      if( this.moveX2 < (this.moveX1) ){
        // this.moveX2 = this.moveX1 + this.handleW;
        // this.x2 = this.moveX1 + this.handleW;
        this.moveX2 = this.moveX1;
        this.x2 = this.moveX2;
      } else if( this.moveX2 > (this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth) ){
        // this.x2 = this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth;
        this.moveX2 = this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth;
        this.x2 = this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth;
      } else {
        this.x2 = this.moveX2;
      }
      // this.value2 = Math.floor(this.moveX2 *  ( ( (this.settings.max_value - this.settings.min_value) / (this.$sliderbar_inner.clientWidth - this.$handle2_wrap.clientWidth) ) ) + this.settings.min_value );

      //--- min_value〜max_valueの範囲に対してのvalueを取得
      this.value2 = this.getValue(this.moveX2);

      //--- %の取得
      // this.rateValue2 = Math.round( this.moveX2 * ( this.settings.rate / this.limitX) );
      this.rateValue2 =  this.getRateValue(this.moveX2);

      //---- step時の処理
      this.setStepHandlePos(this.moveX2, this.$handle2_wrap);

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
      //--- range時のバーの設定
      if(this.settings.type=="range"){
        // this.min_barW = this.moveX1;
        this.max_barW = this.x2;
      }
    },
    setStepHandlePos(_moveVal, _handle){
      //---- step時の処理
      if(this.step != 0){
        console.log('stepあり = ' + _moveVal + ", __handle = " + _handle);
        for (let index = 0; index < this.stepPos_list.length; index++) {
          if( _moveVal < this.stepPos_list[index] ){
            if( _moveVal < ( this.stepPos_list[index - 1] + this.stepPos_list[index] ) / 2 ){
              if( this.handleType == 0){
                this.x1 = this.stepPos_list[index - 1];
                this.stepValue1 = this.stepValue_list[index - 1];
                // this.rateValue1 =  this.getRateValue(this.x1 + (this.$handle1_wrap.clientWidth / 2) );
                this.rateValue1 =  this.getRateValue(this.x1);
                this.value1 = this.stepValue1;
              } else {
                this.x2 = this.stepPos_list[index - 1];
                this.stepValue2 = this.stepValue_list[index - 1];
                // this.rateValue2 =  this.getRateValue(this.x2 + (this.$handle2_wrap.clientWidth / 2));
                this.rateValue2 =  this.getRateValue(this.x2 );
                this.value2 = this.stepValue2;
              }
            } else {
              if( this.handleType == 0){
                this.x1 = this.stepPos_list[index];
                this.stepValue1 = this.stepValue_list[ index ];
                // this.rateValue1 =  this.getRateValue(this.x1 + (this.$handle1_wrap.clientWidth / 2));
                this.rateValue1 =  this.getRateValue(this.x1);
                if( this.x1 > (this.$sliderbar_inner.clientWidth - _handle.clientWidth) ){
                  this.x1 = this.$sliderbar_inner.clientWidth - _handle.clientWidth;
                }
                this.value1 = this.stepValue1;
              } else {
                this.x2 = this.stepPos_list[index];
                this.stepValue2 = this.stepValue_list[ index ];
                // this.rateValue2 =  this.getRateValue(this.x2 + (this.$handle2_wrap.clientWidth / 2));
                this.rateValue2 =  this.getRateValue(this.x2 );
                if( this.x2 > (this.$sliderbar_inner.clientWidth - _handle.clientWidth) ){
                  this.x2 = this.$sliderbar_inner.clientWidth - _handle.clientWidth;
                }
                this.value2 = this.stepValue2;
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

    getValue( _moveX){
      return  Math.round(_moveX * ( this.settings.max_value / (this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth) ));
    },

    getRateValue( _moveX){
      console.log('rate X = ' + _moveX + ", rate = " + this.settings.rate + ", limitX = " + this.limitX);
      // return Math.round( _moveX * ( this.settings.rate / this.limitX) );
      // return Math.round( _moveX * ( this.settings.rate / (this.limitX + this.$handle1_wrap.clientWidth ) ) );
      // return Math.round( _moveX * ( this.settings.rate / (this.limitX + this.$handle1_wrap.clientWidth - this.$handle1_wrap.clientWidth ) ) );
      return Math.round( _moveX * ( this.settings.rate / (this.limitX) ) );
    },

    //---- InitValueが設定されていて、tick_ValueListが設定されている場合、マッチするValueを取得
    setInitStepValue( _type, _initVal ){
      for (let index = 0; index < this.stepValue_list.length; index++){
         if( _initVal == this.stepValue_list[ index ] ){
            if(_type == 1){
              this.moveX1 = this.stepPos_list[index];
            } else {
              this.moveX2 = this.stepPos_list[index];
            }
           break;
         }
      }
    },

    //---- InitValueが設定されている場合のValueを取得
    setInitValue( _type, _initVal){
      if( _type == 1){
        this.moveX1 = _initVal * (this.$sliderbar_inner.clientWidth / this.settings.max_value);
        if(this.moveX1 >= (this.$sliderbar_inner.clientWidth - this.$handle1_wrap.clientWidth) ){
          //---- ハンドルのz-index設定
          this.handle1_z = 2;
          this.handle2_z = 1;
        }
      } else {
        this.moveX2 = _initVal * (this.$sliderbar_inner.clientWidth / this.settings.max_value);
        if(this.moveX2 <= (0) ){
          //---- ハンドルのz-index設定
          this.handle1_z = 1;
          this.handle2_z = 2;
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
  --handle_w : 10px;
  --handle_h : 20px;
  --handle_r : 0px;
  --handle1_c: rgba(0,0,0,1);
  --handle2_c: "#CCC";
  --gageArea_c : #000;
  --gageArea_r : 0px;
  --gageArea_h : 20px;
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
  // border-radius: var(--gageArea_r);
  background-color: var(--activebar_c);
}
.gageArea{
  // height: 20px;
  width: 100%;
  height: var(--gageArea_h);
  border-radius: var(--gageArea_r);
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
  top: 50%;
  transform: translate(0, -50%);

//  .handle1{
//     background-color: var(--handle1_c);
//   }
}

.handle2_wrap{
  position: absolute;
  top: 50%;
  transform: translate(0, -50%);
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
  img{
    user-select: none;
  }
  width: var(--handle_w);
  height: var(--handle_h);
  border-radius: var(--handle_r);
  &.handle1{
    background-color: var(--handle1_c);
  }
  &.handle2{
    background-color: var(--handle2_c);
  }
}


</style>
