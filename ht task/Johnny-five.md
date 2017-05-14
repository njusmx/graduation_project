##Johnny-five.js

###1.功能

##### 使得前端程序员不需要重新学习Sketch这门新语言,而直接可以使用node.js对Arduino板（Raspberry Pi/Electric Imp/Beagle Bone等）进行编程。
 
###2.实例
##### &nbsp;这是一个较简单的实例,下面的动图是完成效果（动图打不开请翻墙）

<img src="https://www.pubnub.com/wp-content/uploads/2016/05/arduino-johnnyfive-hue.gif" width="400" height="400" alt="led灯变色闪烁"/>

##### &nbsp;首先我们需要以下硬件设备
> 1. Arduino Uno
> 2. 一个RGB LED
> 3. 一个面包板
> 4. 跳线*4
> 5. 若干电阻

##### &nbsp;电路图的组装如上动图,以下是代码
```
<input id="red" type="range" min="0" max="255" step="1" value="0">
<input id="green" type="range" min="0" max="255" step="1" value="0">
<input id="blue" type="range" min="0" max="255" step="1" value="0">
```
##### &nbsp;使用3个分别代表红绿蓝的slider bar控制led的颜色

```
<script src="//cdn.pubnub.com/pubnub-3.14.5.js"></script>
```
##### &nbsp;有很重要的一点是我们要把我们的设备连入互联网使得它成为物联网的一部分。不像是蓝牙有距离的限制,使用IOT,一旦设备被接入网络,你在任何地方都可以操纵该设备。在这个实例中使用了pubnub javascript api,以下是pubnub的初始化代码

```
var pubnub = PUBNUB.init({
　subscribe_key : 'you_subscribe_key',                          
　publish_key   : 'your_publish_key'
});
```
##### &nbsp;当slider bar被拖动,程序发送数据去改变LED的颜色,加监听,一下是红的slider bar的代码,蓝绿同理。
```
var red = document.getElementById('red');
 
red.addEventListener('change', function(){
  pubnub.publish({
    channel: 'smart-led', 
    message: {color: 'red', brightness: +this.value}
  });
}, false);
```
##### &nbsp;注意一点channel的名字是可以随意取的,但是接受和发送代码的channel必须是一致的。

##### &nbsp;以下是对板子的编程的代码,这里用到的是johnny-five
```
var five = require('johnny-five');
var led;
 
five.Board().on('ready', function() {
  console.log('ready');
 
  // Initialize the RGB LED
  led = new five.Led.RGB({
    pins: { // pin numbers对应的是板上的pin

      red: 6,
      green: 5,
      blue: 3
    }
  });
 
  // test
  led.color({red: 255, blue: 0, green: 0});
 
  led.on();
});
```
##### &nbsp;在以上的//test段的代码,LED应该是红色此时

##### &nbsp;以下是LEB接收数据的代码,在回调中let.color(),使得LED灯显示相应的颜色
```
pubnub.subscribe({
  channel: 'smart-led',
  callback: function(m) {
    if(led) {
      r = (m.color === 'red') ? m.brightness : r;
      g = (m.color === 'green') ? m.brightness : g;
      b = (m.color === 'blue') ? m.brightness : b;
 
      led.color({red: r, blue: b, green: g});
 
      console.log( 'color change to...' );
      console.log( led.color() );
    }
  }
  error: function(err) {console.log(err);}
});
```
##### &nbsp;回调中的参数m是slider bar的监听里的message

###3.体系结构
##### 没有

###4.优势与不足
##### 优势在于使用的是node.js,js已经渗透至现代计划领域的每个角落,J5用于几乎所有新型的微处理控制器。
##### 不足我暂时还没发现,没有在具体项目中使用。


