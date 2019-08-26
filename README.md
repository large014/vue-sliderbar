# vue-sliderbar
カスタムスライダーバーを実装するコンポーネント。  
[DEMO](https://large014.github.io/vue-sliderbar/)  

## Install
```
./src/components/slideBar/ をコピー
```

### Usage
```
import Sliderbar from './slideBar/Sliderbar.vue'

//--- Sliderbarを利用する際は、各プロパティを設定してください
<Sliderbar ref="sliderbar" class="sliderbar" :settings="settings" @bar_update="getValue">
    <div slot="handle1" class="handle handle1">
    </div>
    <div slot="handle2" class="handle handle2">
    </div>
</Sliderbar>

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
            gageArea_R : "30px",
            bar_H : "20px",
            handle_W : "20px",
            handle_H : "20px",
            handle_R : "15px",
            handle_min_C : "#3f51b5",
            handle_max_C : "#ff51b5",
            min_value : 0,
            max_value : 500,
            init_value1 : 200,
            init_value2 : 400,
            rate: 100,
            tick_ValueList:["月","火","水","木","金","土","日"],
            isscale : true,
            scale_Step : 3,
            scale_C : "#F00",
            scale_BaseTop : "-10px",
            scale_Top : "-10px",
        }
    }
},

-----------------------------------------------------------

```
・設定プロパティ  
[type] シングルハンドル : 'normal'、レンジハンドル : 'range'。  
[width] スライダーバーUI全体のwidthを設定します。  
[activebar_C] スライダーバーのアクティブバーの背景色を設定します。  
[deactivebar_C] スライダーバーのディアクティブ背景色を設定します。(type : rangeのみ)  
[gageArea_C] スライダーバーのディアクティブ背景色を設定します。(type : normalのみ)  
[gageArea_H] スライダーバーUIのheightを設定します。  
[gageArea_R] スライダーバーUIのborder-radiusを設定します。  
[bar_H] アクティブバー、ディアクティブバーのheightを設定します。。  
[handle_W] ハンドルのwidthを設定します。  
[handle_H] ハンドルのheightを設定します。  
[handle_R] ハンドルのboder-radiusを設定します。  
[handle_min_C] ハンドル1のbackground-colorを設定します。  
[handle_max_C] ハンドル2のbackground-colorを設定します。(type:rangeのみ)  
[min_value] スライダーUIの最小の値を設定します。  
[max_value] スライダーUIの最大の値を設定します。  
[init_value1] ハンドル1の初期位置を設定します。  
[init_value2] ハンドル2の初期位置を設定します。  
[rate] %のrateを設定します。  
[tick_ValueList] スライダーUIに固定値を設定する時に利用します。ex)tick_ValueList:["月","火","水","木","金","土","日"]  
[scale_Step] スライダーUIのスッテプを設定します。  
[isscale] 目盛りを表示するかどうかを設定します。  
[scale_C] 目盛りの色を設定します。  
[scale_BaseTop] 目盛り全体の上下の位置を設定します。  
[scale_Top] 目盛りの上下の位置を設定します。  

・取得プロパティ  
[value1] type:normal時のハンドル位置の値、type:range時のminハンドルの値を取得します  
[value2] type:range時のmaxハンドルの値を取得します  
[rateValue1] type:normal時のハンドルの%値、type:range時のminハンドルの%値を取得します  
[rateValue2] type:range時のmaxハンドルの%値を取得します  
[stepValue1] type:normal時のハンドルのstep値、type:range時のminハンドルのstep値を取得します  
[stepValue2] type:range時のmaxハンドルのstep値を取得します  

・イベント  
@bar_update="〜" を設定する事Sliderbar.vueからUIの更新を取得できます。

```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Run your tests
```
npm run test
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
