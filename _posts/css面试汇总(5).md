css常见考点

## css面试汇总
1.选择器优先级
* ！import
* id选择器
* 类选择器，属性选择器，伪类选择器
* 标签选择器，伪元素选择器
* 通配符选择器
* 继承
* 默认  

2.BFC
* 是什么？block format context，块级格式化上下文，简单来说是一个独立的渲染区域
* 可以干什么？决定了元素如何对其内容进行定位以及与其他元素的相互作用。容器里的子元素不会影响到外面的元素。
* 触发条件？根元素，float！=none， overflow！=visible，position是absolute，fixed，display是inline-block，tabel-cell等
* 常用解决问题？margin重叠问题，清除浮动，自适应多列布局

3.盒模型
w3c标准盒模型：content-box
IE盒模型：border-box
可以用box-sizing属性控制
4.flex布局
* 弹性布局由flex-container以及flex-item弹性子元素构成
* 容器存在两根轴，main axis和cross axis
* 容器的六个属性 
>flex-direction（row | row-reverse | column | column-reverse）决定主轴的方向，即项目的排列方向。  
flex-wrap（nowrap | wrap | wrap-reverse）决定弹性子元素是否换行，其中wrap-reverse表示第一行在下边。  
flex-flow 该属性是flex-direction和flex-wrap的简写形式。默认为row nowrap  
justify-content （flex-start | flex-end | center | space-between | space-around）决定了子元素在主轴上的排列方式  
align-items （flex-start | flex-end | center | baseline | stretch）定义项目在侧轴上的对齐方式，baseline表示项目的第一行文字的基线对齐  
align-content 定义了多根轴线的对齐方式，如果只有一根轴线，该属性不起作用。  
* 项目的六个属性 
>order 定义项目的排列顺序，数值越小排列越靠前  
flex-grow 定义项目的放大比例，该数值表示剩余空间如何分配，默认为0  
flex-shrink 定义了项目的缩小比例，默认为1。如果空间不足，项目将缩小  
flex-basis 定义了在分配多余空间之间，项目占据的主轴空间。默认为auto，即项目的本来大小。  
flex 是flex-grow，flex-shrink 和flex-basis的简写，默认为0 1 auto，后两个属性 
 为可选值。该属性有两个快捷值，auto(1,1,auto) 和 none(0,0,auto)  
align-self 允许单个项目和其他项目有不一样的对齐方式，可覆盖align-items  

4.伪类和伪元素的区别
* 伪类的操作对象是文档树中已有的元素，而伪元素则创建了一个文档树外的元素。因此两者区别在于有没有创建一个文档树之外的元素。 一般伪元素在css规范中要求使用双冒号。  
