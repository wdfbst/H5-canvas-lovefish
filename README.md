# H5-canvas-lovefish
使用H5实现爱心形大小鱼的游动，通过吃海葵果实延长游戏结束周期<br/>
下面是HTML爱心小鱼游戏的学习总结，主要涉及了html5 canvas的API使用, 轮播序列帧动画，碰撞检测，贝塞尔曲线以及一些游戏技巧的学习<br/>
一、页面搭建
把画布分为上下两层，用z-index设置<br/>
上层canvas1绘制大小鱼,漂浮物,分值UI,特效 <br/>
下层canvas2绘制背景，海葵，果实<br/>
建立main.js文件，里面存放对游戏初始化，循环控制等函数。要注意动作类的放在循环内部每次循环都执行到，而属性类的API放在循环外面避免重复执行。<br/>
二、画静止的海葵<br/>
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
三、海葵产生果实<br/>
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

