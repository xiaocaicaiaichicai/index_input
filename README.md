 #index_input  
修改input自动填充的底色  
input可能是平时网页中使用最多的标签之一了，但凡需要输入的地方都少不了。  
毕竟输入是一件比较麻烦的事情，所以浏览器也做出了许多便于输入的地方，比方说自动填充  


一般情况下，在表单提交的时候浏览器会自动记录提交的内容  

这个功能本来是很好的，不过当自动填充后，输入框背景会自动变成淡紫色（以前是x黄色，仅限谷歌浏览器），设计师（但凡有点审美的前端）看到了肯定就不干了，毕竟还是挺违和的，那么何如去除这个默认的背景呢？  
背景是如何产生的  
按照以往的经验，如果要修改标签默认的样式，首先肯定是查看标签的默认样式，如下  

比较好理解，当自动填充时会激活:-internal-autofill-selected伪类，然后就变成了淡紫色。  
如果我们设置样式覆盖  
input:-internal-autofill-selected{  
    background: red!important;  
}  
复制代码虽然样式覆盖了，然而并没有什么用，仍然是默认的淡紫色（要是起作用的话就不会专门去研究这个了），原因不明...  
现有的解决方式  
网上普遍的解决方式主要有两种：  
1. 使用内阴影进行覆盖  
既然背景改变不了，那么就找个东西覆盖呗  
input{  
    box-shadow: 0 0 0px 1000px white inset;  
}  

大部分情况使用这种方式即可解决问题  
2. 关闭自动填充功能  
既然是由于自动填充导致的问题，关掉就行了呀  
<input  autocomplete="off">  

有些对信息比较敏感的情况下（不想自动填充）可以使用这个功能  
现有解决方式的局限   
首先说第二种方式，虽然这种方式比较简单，但是却失去了自动填充功能，这对于用户体验是很不友好的（手动输入是一件费时费力的事情），如果说要自定义这个功能，那可能还需要不少的js脚本（还需考虑键盘访问），而且可能还会有意料之外的bug，所以一般情况下不建议去除默认功能。  
那么第一种方式呢？如果输入框是纯色的背景，那么给一个和背景一样的阴影颜色就好了，如果而我们设计需要的是一种透明的输入框，那基本就凉掉了。  

比如类似这样的效果，如果被填充了还是相当难看的  

更好的解决方式  
下面给出两种解决方式  
方式一：利用background-clip: content-box  
既然背景改变不了，那么就把它裁掉呗。  
背景颜色默认是渲染到padding-box的，我们可以设置background-clip: content-box只渲染到content-box，这样背景就看不到了（当然还需指定一下高度为0。）~  
input{  
  height: 0;  
  padding: 1.2em .5em;  
  background-clip: content-box;  
}  

可以看到，自动填充时的背景已经消失了，不过还有一个小问题，填充的文字颜色也是无法直接修改的（默认为rgb(0,0,0)），原因相同，这里我们可以借助一下::first-line伪类  
input::first-line{  
  color: #fff  
}  

应该算比较完美了~  

如果demo里面没有自动填充选项，可以先输入一些字符，然后submit，手动创建一些填充项，下同  
方式二：利用animation-fill-mode:forwards  
设置animation-fill-mode:forwards后，动画会一直停留在最后一帧，这个已经和默认的样式不是一个维度了，不管设置什么样式，都会保留最后一帧状态，比如  
<style>  
div{  
    animation:resetBg .1s forwards;  
}  
@keyframes resetBg {  
  to {  
    background: blue;  
  }  
}  
</style>  
<div style="background:red!important">div</div>  
复制代码  
可以看到背景很轻易的就被改成了蓝色。  
利用这个特性，上面的问题就很容易了，只需设置一个动画即可  
input{  
  animation: resetBg .1s forwards;  
}  
@keyframes resetBg {  
  to {  
    color: #fff;  
    background: transparent;  
  }  
}  
