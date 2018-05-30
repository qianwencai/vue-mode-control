<template>
  <div class="hello">
    <div id="selectModel"
         style="position:relative;display:block;text-align:center;position:absolute;width:100%">
      <div>
        <span ref="txtmodel" id="txtmodel"
              style="font-size:1rem;display:block;color:white;text-shadow: 0 0 10px #FFFFFF;margin-top: .3rem">Cool</span>
      </div>
      <section class="container_mode">
        <div ref="carousel_mode" id="carousel_mode" @touchstart="touchstart_mode"  @touchmove='touchmove_mode' @touchend="touchend_mode" style="transform: translateZ(-523px) rotateY(-90deg);" class="panels-backface-invisible">
          <figure @touchstart="changeMode" id="mode_1"
                  style="opacity: 1; transform: rotateY(0deg) translateZ(523px); background-color: none;">
            <img src="./images/index_mode_auto.png" style="height:1.5rem"/>
          </figure>
          <figure  @touchstart="changeMode" id="mode_2"
                  style="opacity: 1; transform: rotateY(22.5deg) translateZ(523px); background-color: none;">
            <img src="./images/index_mode_cool.png" style="height:1.5rem"/>
          </figure>
          <figure  @touchstart="changeMode" id="mode_3"
                  style="opacity: 1; transform: rotateY(45deg) translateZ(523px); background-color: none;">
            <img src="./images/index_mode_dry.png" style="height:1.5rem"/>
          </figure>
          <figure @touchstart="changeMode" id="mode_4"
                  style="opacity: 1; transform: rotateY(67.5deg) translateZ(523px); background-color: none;">
            <img src="./images/index_mode_fan.png" style="height:1.5rem"/>
          </figure>
          <figure @touchstart="changeMode" id="mode_5"
                  style="opacity: 1; transform: rotateY(90deg) translateZ(523px); background-color: none;">
            <img src="./images/index_mode_heat.png" style="height:1.5rem"/>
          </figure>

          <figure @touchstart="changeMode" id="mode_6"
                  style="opacity: 1; transform: rotateY(0deg) translateZ(523px); background-color: none;">
            <img src="./images/index_mode_auto.png" style="height:1.5rem"/>
          </figure>
          <figure  @touchstart="changeMode" id="mode_7"
                  style="opacity: 1; transform: rotateY(22.5deg) translateZ(523px); background-color: none;">
            <img src="./images/index_mode_cool.png" style="height:1.5rem"/>
          </figure>
          <figure @touchstart="changeMode" id="mode_8"
                  style="opacity: 1; transform: rotateY(45deg) translateZ(523px); background-color: none;">
            <img src="./images/index_mode_dry.png" style="height:1.5rem"/>
          </figure>
          <figure   @touchstart="changeMode" id="mode_9"
                  style="opacity: 1; transform: rotateY(67.5deg) translateZ(523px); background-color: none;">
            <img src="./images/index_mode_fan.png" style="height:1.5rem"/>
          </figure>
          <figure  @touchstart="changeMode" id="mode_10"
                  style="opacity: 1; transform: rotateY(90deg) translateZ(523px); background-color: none;">
            <img src="./images/index_mode_heat.png" style="height:1.5rem"/>
          </figure>

        </div>
      </section>

    </div>
  </div>
</div>
</template>

<script>
  //横滚控件
  function Carousel(el) {
    this.element = el;

    this.rotation = 0;
    this.panelCount = 0;
    this.totalPanelCount = this.element.children.length;
    this.theta = 0;
    this.panelWith = 0;
    this.isHorizontal = true;
  }

  Carousel.prototype.modify = function (isReset) {
    var panel, angle, i;

    this.panelWith = this.panelSize = this.element[this.isHorizontal ? 'offsetWidth' : 'offsetHeight'];

    this.rotateFn = this.isHorizontal ? 'rotateY' : 'rotateX';
    this.theta = 360 / this.panelCount;

    // do some trig to figure out how big the carousel
    // is in 3D space
    this.radius = Math.round(( this.panelSize / 2) / Math.tan(Math.PI / this.panelCount));

    for (i = 0; i < this.panelCount; i++) {
      panel = this.element.children[i];
      angle = this.theta * i;
      panel.style.opacity = 1;
      // panel.style.backgroundColor = 'hsla(' + angle + ', 100%, 50%, 0.8)';
      // rotate panel, then push it out in 3D space
      panel.style['transform'] = this.rotateFn + '(' + angle + 'deg) translateZ(' + this.radius + 'px)';
    }

    // hide other panels
    // for (  ; i < this.totalPanelCount; i++ ) {
    //     panel = this.element.children[i];
    //     panel.style.opacity = 0;
    //     panel.style[ transformProp ] = 'none';
    // }

    // adjust rotation so panels are always flat
    this.rotation = Math.round(this.rotation / this.theta) * this.theta;

    this.transform();
  };

  Carousel.prototype.transform = function () {
    this.element.style['transform'] = 'translateZ(-' + this.radius + 'px) ' + this.rotateFn + '(' + this.rotation + 'deg)';
  };
export default {
  name: 'HelloWorld',
  data () {
    return {
      carousel:'',
      Selectmode : 1,//当前选择值
      identifier : -1,//手指唯一id
      isEditTime : '',
      isEdit : '',
      lastX_mode : 0, // 上一次位置
      lastMoveStart_mode : 0,
      lastMoveTime_mode : 0,
      StartX_mode : 0,// 初始位置
      fadeOutTime : 1000,//滑动后其它选择消失时间
      lastcarouselrotation_mode : 0,//最后温度滚动角度
      CurrentMode : 0,
      modeCarousel:'',
      ACMode:{ //模式定义
        cool:1,
        auto:0,
        heat: 4,
        fan: 3,
        dry: 2,
        energy: 5
      }
    }
  },
  watch: {
    CurrentMode() {
      //普通的watch监听,CurrentMode变化时触发
      this.setModeTxt();
    },
  },
  mounted(){
    this.init_Mode();
    this.setMode(this.CurrentMode);
  },
  methods:{
    init_Mode() {
      this.carousel = new Carousel(document.getElementById('carousel_mode'));
      this.modeCarousel = this.carousel;
      this.carousel.panelCount = 10;
      this.carousel.modify();
      let carousel_mode =  document.getElementById('carousel_mode');
      let figures = carousel_mode.getElementsByTagName('figure');
      for(var i=0;i<figures.length;i++){
        figures[i].style.width = this.carousel.panelWith + "px";
      }
      for (var i = 1; i <= 10; i++) {
        if (i != this.Selectmode) {

        }
      }
      this.setModeTxt();
    },
    touchstart_mode(event){
      if (this.identifier != -1) {
        return;
      }
      if (event.targetTouches.length > 1) {
        return;
      }
      this.identifier = event.changedTouches[0].identifier;
      if (event.changedTouches[0].identifier != this.identifier) {
        return;
      }
      this.isEdit = true;
      this.isEditTime = event.timeStamp || Date.now();
      if (event.targetTouches.length == 1) {
        event.preventDefault();// 阻止浏览器默认事件，重要
        var touch = event.targetTouches[0];
        this.lastMoveStart_mode = this.StartX_mode = this.lastX_mode = touch.pageX;
        this.lastMoveTime_mode = event.timeStamp || Date.now();
        this.initR_mode = this.carousel.rotation;
        let carousel_mode =  document.getElementById('carousel_mode');
        let figures = carousel_mode.getElementsByTagName('figure');
        carousel_mode.style.transition = 'transform 0s';
        for(var i=0;i<figures.length;i++){
          figures[i].style.transition = 'transform 0s';
        }
//        for (var i = 1; i <= 10; i++) {
//          var panel = $("#mode_" + i);
//          panel.stop();
//          panel.css("opacity", "1");
//          panel.show();
//        }
      }
    },
    touchmove_mode(event){
      if (event.changedTouches[0].identifier != this.identifier) {
        return;
      }
      this.isEditTime = event.timeStamp || Date.now();
      if (event.targetTouches.length == 1) {
        var touch = event.targetTouches[0];
        var nowX = event.touches[0].pageX;
        var dir = (nowX - this.lastX_mode) > 0 ? 1 : -1;
        this.lastX_mode = nowX;
        //根据move移动距离移动数字
        var moveX = nowX - this.StartX_mode;
        var valpresect = (moveX / this.carousel.panelWith) * this.carousel.theta;
        var ChangeRotate = valpresect;
        ChangeRotate = parseInt(ChangeRotate);
        if (this.carousel.rotation != ChangeRotate) {
          this.carousel.rotation = this.initR_mode + ChangeRotate;
          this.carousel.transform();
        }
        /**
         * 缓动代码
         */
        var nowTime = event.timeStamp || Date.now();
        if (nowTime - this.lastMoveTime_mode > 300) {
          this.lastMoveTime_mode = nowTime;
          this.lastMoveStart_mode = nowX;
        }
      }
    },
    touchend_mode(event){
        if (event.changedTouches[0].identifier != this.identifier) {
          return;
        }
        this.identifier = -1;
      this.isEdit = false;
      this.isEditTime = event.timeStamp || Date.now();
      let carousel_mode =  document.getElementById('carousel_mode');
      let figures = carousel_mode.getElementsByTagName('figure');
      carousel_mode.style.transition = 'transform 0.2s';
      for(var i=0;i<figures.length;i++){
        figures[i].style.transition = 'transform 0.2s';
      }
        var moveX = this.lastX_mode - this.lastMoveStart_mode;
        var checkMove = this.lastX_mode - this.StartX_mode;
        var checkDis = Math.abs(checkMove);
        if (checkDis == 0) {
          for (var i = 1; i <= 10; i++) {
            if (i != this.Selectmode) {
//              var panel = $("#mode_" + i);
  //                            panel.fadeOut(fadeOutTime);
            }
          }
          return;
        }
        /**
         * 缓动代码
         */
        var nowTime = event.timeStamp || Date.now();
        var v = moveX / (nowTime - this.lastMoveTime_mode); //最后一段时间手指划动速度
        var dir = v > 0 ? -1 : 1; //加速度方向
        var deceleration = dir * 0.0006;
        var duration = v / deceleration; // 速度消减至0所需时间
        var dist = v * duration / 2; //最终移动多少
        var val = parseInt(dist / (moveX * 2));
        if (val > 15 || val < -15) {
          val = -1 * 15 * dir;
        }
        if (isNaN(val))
          val = 0;
        //根据方向补剩余移动量
        var tmp = (this.carousel.theta + this.carousel.rotation % this.carousel.theta) % this.carousel.theta;
        if (dir == 1) {//手左滑
          //不够20%不移动
          if ((tmp * 100 / this.carousel.theta) < 80) {
            this.carousel.rotation = this.carousel.rotation - tmp;
            this.carousel.rotation += Math.abs(val) * dir * -1 * this.carousel.theta;
          } else {
            //复原
            this.carousel.rotation = this.carousel.rotation + (this.carousel.theta - Math.abs(tmp));
          }
        }
        else {//手右滑
          tmp = this.carousel.theta - Math.abs(tmp);
          if ((tmp * 100 / this.carousel.theta) < 80) {
            this.carousel.rotation = this.carousel.rotation + tmp;
            this.carousel.rotation += Math.abs(val) * dir * -1 * this.carousel.theta;
          } else {
            this.carousel.rotation = this.carousel.rotation - (this.carousel.theta - Math.abs(tmp));
          }

        }
      this.carousel.transform();
      this.lastcarouselrotation_mode = this.carousel.rotation;

        var roleval = this.carousel.rotation / this.carousel.theta;
        //console.log(carousel.rotation);
        if (roleval > 0) {
          this.Selectmode = 1 + 10 - roleval % 10;
        }
        else if (roleval < 0) {
          this.Selectmode = 1 + roleval % 10 * -1;
        }
        else {
          this.Selectmode = 1;
        }
        if (this.Selectmode > 0) {
          this.Selectmode += 0.1;
        }
        else if (this.Selectmode < 0) {
          this.Selectmode -= 0.1;
        }
        let Selectmode = parseInt(this.Selectmode);
        if (Selectmode <= 0) {
          this.Selectmode = 10;
        }
        if (Selectmode >= 11) {
          this.Selectmode = 1;
        }
        //console.log(Selectmode);
        if (Selectmode == 1 || Selectmode == 6) {
          this.CurrentMode = this.ACMode.auto;
        }
        else if (Selectmode == 2 || Selectmode == 7) {
          this.CurrentMode = this.ACMode.cool;
        }
        else if (Selectmode == 3 || Selectmode == 8) {
          this.CurrentMode = this.ACMode.dry;
        }
        else if (Selectmode == 4 || Selectmode == 9) {
          this.CurrentMode = this.ACMode.fan;
        }
        else if (Selectmode == 5 || Selectmode == 10) {
          this.CurrentMode = this.ACMode.heat;
        }

        console.log('this.CurrentMode:'+this.CurrentMode);

        for (var i = 1; i <= 10; i++) {
          if (i != this.Selectmode) {
//            var panel = $("#mode_" + i);
  //                        panel.fadeOut(fadeOutTime);
          } else {
//            var panel = $("#mode_" + i);
  //                        panel.stop();
  //                        panel.fadeIn(200);
          }
        }
  //                if (isIlegalAndTip(CurrentMode)) {
  //                    try {
  //                        ChangeMode(CurrentMode);
  //                    } catch (e) {
  //                    }
  //                    if (g_CurrentMode != CurrentMode) {
  //                        g_CurrentMode = CurrentMode;//改变全局 、风速要使用到这个值
  //                        // DataObject.Mod = CurrentMode;
  //                        DataObject.SwhSlp = 0;
  //                        DataObject.SlpMod = 0;
  //                        //处理发送修改的数据给设备
  //                        // var jsonData = "{\"opt\":[\"Mod\",\"SwhSlp\",\"SlpMod\"],\"p\":[" + CurrentMode + "," + DataObject.SwhSlp + "," + DataObject.SwhSlp + "],\"t\":\"cmd\"}";
  //                        var data = mutexManager.getModeCmd(CurrentMode);
  //                        var obj = JSON.parse(data);
  //                        var jsonData = JSON.stringify(obj.cmd);
  ////                        updateFromCmd(obj.cmd,1);
  //                        // btnManager.setAdvanceValue(DataObject.Air, DataObject.Blo, DataObject.Health, DataObject.Lig,
  //                        //     DataObject.SvSt, DataObject.SwUpDn, DataObject.SwingLfRig, DataObject.StHt, DataObject.SwhSlp, DataObject.Pow);
  //
  //                        var imgSleep = $("#imgSleep");
  //                        imgSleep[0].src = "../../Public/images/ac/advance_n_sleep.png";
  //                        $("#modeSleep").hide();
  //                        //集中控制不回调，直接修改全局DataObject
  //                        if (g_functype == FuncType.Mode_Control) {
  //                            flag=false;
  //                            callback(jsonData, true);
  //
  //                        }
  //
  //                    }
  //                } else {
  //                    setTimeout(function(){
  ////                        CurrentMode = g_CurrentMode;
  ////                        ChangeMode(CurrentMode);
  ////
  ////                        modeManager.setMode(CurrentMode);
  //                    },300);
  //                }
    },
    changeMode(e){
      let str = e.currentTarget.id;
      let sed = str.indexOf('_');
      let index = str.substring(sed+1,str.length)
      if(index>5){
        index = index-5;
      }
      this.CurrentMode = index-1;
      console.log('this.CurrentMode:'+this.CurrentMode);
      this.carousel.rotation = (-(index - 1) * 36 );
      this.carousel.transform();

    },
    setMode(mode) {
      let carousel_mode =  document.getElementById('carousel_mode');
      let figures = carousel_mode.getElementsByTagName('figure');
      carousel_mode.style.transition = 'transform 0s';
      for(var i=0;i<figures.length;i++){
        figures[i].style.transition = 'transform 0s';
      }
      //滚动到对应角度
      this.Selectmode = mode + 1;
      this.modeCarousel.rotation = -1 * this.modeCarousel.theta * (parseInt(mode));
      this.modeCarousel.transform();

//      for (var i = 1; i <= 10; i++) {
//        var panel = $("#mode_" + i);
//        if (i != Selectmode) {
////                    panel.hide();
//        }
//        else {
//          //panel.show();
//          panel.stop();
//          panel.css("opacity", "1");
//          panel.show();
//        }
//      }
    },
    setModeTxt(){
      let modeTxt = '';
      switch (this.CurrentMode){
        case 0:
          modeTxt = '自动';
          break;
        case 1:
          modeTxt = '制冷';
          break;
        case 2:
          modeTxt = '除湿';
          break;
        case 3:
          modeTxt = '扫风';
          break;
        case 4:
          modeTxt = '制热';
          break;
        default:
          break;
      }
      this.$refs.txtmodel.innerText = modeTxt;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
