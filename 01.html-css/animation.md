## transition



## animation

## 区别
CSS 中transition和animate有何区别?
transition一般用来做过渡的, 没时间轴的概念, 通过事件触发(一次),没中间状态(只有开始和结束)
而animation则是做动效,有时间轴的概念(帧可控),可以重复触发和有中间状态;
过渡的开销比动效小,前者一般用于交互居多,后者用于活动页居多;
animation-fill-mode: forwards;  

JS可以精准的控制动画的进行


## GSAP



## lib



## 优化

JS动画
缺点：(1)JavaScript在浏览器的主线程中运行，而主线程中还有其它需要运行的JavaScript脚本、样式计算、布局、绘制任务等,对其干扰导致线程可能出现阻塞，从而造成丢帧的情况。
        (2)代码的复杂度高于CSS动画
优点：(1)JavaScript动画控制能力很强, 可以在动画播放过程中对动画进行控制：开始、暂停、回放、终止、取消都是可以做到的。
        (2)动画效果比css3动画丰富,有些动画效果，比如曲线运动,冲击闪烁,视差滚动效果，只有JavaScript动画才能完成
        (3)CSS3有兼容性问题，而JS大多时候没有兼容性问题
CSS动画
缺点：
   (1)运行过程控制较弱,无法附加事件绑定回调函数。CSS动画只能暂停,不能在动画中寻找一个特定的时间点，不能在半路反转动画，不能变换时间尺度，不能在特定的位置添加回调函数或是绑定回放事件,无进度报告
   (2)代码冗长。想用 CSS 实现稍微复杂一点动画,最后CSS代码都会变得非常笨重。

优点： (1)浏览器可以对动画进行优化。
  ●  浏览器使用与 requestAnimationFrame 类似的机制，requestAnimationFrame比起setTimeout，setInterval设置动画的优势主要是:1)requestAnimationFrame 会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成,并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率,一般来说,这个频率为每秒60帧。2)在隐藏或不可见的元素中requestAnimationFrame不会进行重绘或回流，这当然就意味着更少的的cpu，gpu和内存使用量。
  ● 强制使用硬件加速 （通过 GPU 来提高动画性能）
 
CSS动画流畅的原因
渲染线程分为main thread(主线程)和compositor thread(合成器线程)。
如果CSS动画只是改变transform和opacity，这时整个CSS动画得以在compositor thread完成（而JS动画则会在main thread执行，然后触发compositor进行下一步操作）
在JS执行一些昂贵的任务时，main thread繁忙，CSS动画由于使用了compositor thread可以保持流畅，
在主线程中，维护了一棵Layer树（LayerTreeHost），管理了TiledLayer，在compositor thread，维护了同样一颗LayerTreeHostImpl，管理了LayerImpl，这两棵树的内容是拷贝关系。因此可以彼此不干扰，当Javascript在main thread操作LayerTreeHost的同时，compositor thread可以用LayerTreeHostImpl做渲染。当Javascript繁忙导致主线程卡住时，合成到屏幕的过程也是流畅的。
为了实现防假死，鼠标键盘消息会被首先分发到compositor thread，然后再到main thread。这样，当main thread繁忙时，compositor thread还是能够响应一部分消息，例如，鼠标滚动时，加入main thread繁忙，compositor thread也会处理滚动消息，滚动已经被提交的页面部分（未被提交的部分将被刷白）。
CSS动画比JS流畅的前提：
  ● JS在执行一些昂贵的任务
  ● 同时CSS动画不触发layout或paint
在CSS动画或JS动画触发了paint或layout时，需要main thread进行Layer树的重计算，这时CSS动画或JS动画都会阻塞后续操作。
     只有如下属性的修改才符合“仅触发Composite，不触发layout或paint”：
  ● backface-visibility
  ● opacity
  ● perspective
  ● perspective-origin
  ● transfrom
所以只有用上了3D加速或修改opacity时，css3动画的优势才会体现出来。
     (2)代码相对简单,性能调优方向固定
     (3)对于帧速表现不好的低版本浏览器，CSS3可以做到自然降级，而JS则需要撰写额外代码
结论
 如果动画只是简单的状态切换，不需要中间过程控制，在这种情况下，css动画是优选方案。它可以让你将动画逻辑放在样式文件里面，而不会让你的页面充斥 Javascript 库。然而如果你在设计很复杂的富客户端界面或者在开发一个有着复杂UI状态的 APP。那么你应该使用js动画，这样你的动画可以保持高效，并且你的工作流也更可控。所以，在实现一些小的交互动效的时候，就多考虑考虑CSS动画。对于一些复杂控制的动画，使用javascript比较可靠。