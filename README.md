# H5-canvas-lovefish
游戏效果:使用H5实现爱心形大小鱼的游动，小鱼跟随鱼妈妈，大鱼通过吃海葵果实喂养小鱼，延长游戏结束周期<br/>
下面是HTML爱心小鱼游戏的学习总结，主要涉及了html5 canvas的API使用, 轮播序列帧动画，碰撞检测，贝塞尔曲线以及一些游戏技巧的学习<br/>

<imag src="./src/lovefish.png"/>
<b>一、页面搭建</b><br/>
把画布分为上下两层，用z-index设置<br/>
上层canvas1绘制大小鱼,漂浮物,分值UI,特效 <br/>
下层canvas2绘制背景，海葵，果实<br/>
建立main.js文件，里面存放对游戏初始化，循环控制等函数。要注意动作类的放在循环内部每次循环都执行到，而属性类的API放在循环外面避免重复执行。<br/>

<b>二、画静止的海葵</b><br/>
海葵的思路：<br/>
先设定海葵的数量N，<br/>
X坐标：随机取N个坐标值，<br/>
Y坐标：画布的高度减去海葵的长度len，<br/>
然后利用canvas的API绘制，最后需要在main.js中用gameloop()函数不断的循环绘制，进而每次加载随机海葵数量并绘制出海葵。<br/>
API学习笔记：<br/>
1、	ctx2.save() 和 ctx2.restore()<br/>
作用是让在这一对API之间的方法只能作用于这对API之内，出了区间范围就不起作用<br/>
2、	ctx2.globalAlpha<br/>
设置或返回绘图的当前透明值，属性值是[0,1]间的数字。<br/>
3、	ctx2.strokeStyle = “white”<br/>
strokeStyle 属性设置或返回用于笔触的颜色、渐变或模式。注意放在stroke之前<br/>
4、	ctx2.stroke();<br/>
stroke() 方法会实际地绘制出通过 moveTo() 和 lineTo() 方法定义的路径。默认颜色是黑色。<br/>

<b>三、海葵产生果实</b><br/>
果实分为蓝色和橙色果实，果实的随机产生，<br/>
果实出生在海葵头部，位置要确定在海葵头部，<br/>
果实有逐渐长大的变化，设置时延量（++过程）<br/>
果实长大后（设置一极限值，不要一直增大），漂浮起来状态（--过程）<br/>
区分蓝色和橙色果实：设置fruitType检测<br/>
var ran = Math.random();   //随机果实类型<br/>
if(ran < 0.3) {<br/>
    this.fruitType[i] = "blue";<br/>
}else {<br/>
    this.fruitType[i] = "orange";<br/>
}<br/>
果实被大鱼吃掉或飘出：当果实出生时，alive设置为true,<br/> 当被吃掉时(通过碰撞检测)或者果实的坐标值超出画布的高度时，将alive设置为false,可以将这个alive值传给if语句用于判断是否去画果 实，即true状态时就在画布上画果实(长大，漂浮),false状态时，if就不执行画果实。<br/>
何时继续产生果实：当屏幕不足规定个数时，可定义fruitMonitor函数，当alive记录为true时，记录数量，小于某个值则继续产生果实。<br/>

<b>四、画大鱼小鱼及其旋转跟随</b><br/>
类的属性包括：位置坐标、旋转角度、组成结构图片（眼睛、身体、尾巴）<br/>
方法：初始化、绘制(包括鱼妈妈跟随鼠标，小鱼跟随大鱼，大小鱼的旋转)<br/>
具体重要细节笔记有：<br/>
1、	实现大鱼跟随鼠标移动，先获取鼠标的坐标，可以在上层画布上(绘制鱼的层)使用addEventListener()函数侦听onMouseOver函数，事件名称为mousemove<br/>
 function onMouseMove(e) {  <br/>
    if( !data.gameOver ) {  //表示游戏没有结束<br/>
      if(e.offSetX || e.layerX) {//浏览器的兼容性<br/>
使用layerX和layer来兼容Firefox浏览器，其他的浏览器都可以用offset来实现。<br/>
        // 获取鼠标坐标<br/>
        mx = e.offSetX === undefined ? e.layerX : e.offSetX;<br/>
        my = e.offSetY === undefined ? e.layerY : e.offSetY;<br/>
      }<br/>
    }<br/>
}<br/>
2、	大鱼坐标趋于鼠标<br/>
 lerpDistance()函数，这个函数的作用是使一个值趋近于一个目标值：<br/>



	function lerpDistance(aim, cur, radio) {<br/>
  var delta = cur - aim;   //aim:目标值， cur:当前值， radio:百分比<br/>
  return aim + delta * radio;<br/>
}<br/>
举个例子： 目标值是30， 当前值是50， 趋近程度90%， 即：30+(50-30)*0.9=48, 返回的值可以趋近于当前值50.<br/> 它的作用是可以使得动画变得更加平滑。<br/>
3、	大鱼随着鼠标旋转<br/>
 用到的知识点就是Math.atan2(y, x)函数，即Javascript反正切函数：<br/>
Math.atant2(y, x)返回的是正X轴和点 (x, y) 与原点连线之间的角度，这个角度是一个 -pi 到 pi 之间的弧度，表示点 (x, y) 对应的偏移角度。这是一个逆时针角度，以弧度为单位。<br/>
注意此函数接受的参数：先传递 y 坐标，然后是 x 坐标。还需要注意的是 atan2 接受单独的 x 和 y 参数，而 atan 接受两个参数的比值。
然后使用rotate函数根据所求的角度旋转：<br/>
ctx.rotate(angle)旋转当前绘图，其中angle表示旋转的角度，以弧度计算.<br/>
4、小鱼跟随大鱼，小鱼旋转的操作与上述类似，把目标对象换成大鱼就可以了<br/>
其余就是动画效果及游戏分值计算，需再整理上传。
