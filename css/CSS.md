1. 背景属性简写，简写顺序：
background-color
background-image
background-repeat
background-attachment
background-position
    
> 例如：body {background:#ffffff url('img_tree.png') no-repeat right top;}

2. background-attachment背景图像固定，其取值：scroll、fixed，inherit
3. background-blend-mode背景混合（颜色与图片叠加），取值： normal|multiply|screen|overlay|darken|lighten|color-dodge|saturation|color|luminosity;
4. background-clip背景与边框的关系，取值: border-box|padding-box|content-box;
5. background-origin在指定background-position时，限制背景与边框的相对位置: padding-box|border-box|content-box;
6. background-position，取值如下（一组值），也可以是像素值，百分比，只定义一个值时，另一个值取50%

> left top
left center
left bottom
right top
right center
right bottom
center top
center center
center bottom
7. border-style边框的样式，取4个值时，分别指定上右下左，类似：dotted solid double dashed;3值时，是上，左右，下，2值时代表上下
8. clear属性，指定段落的左侧或右侧不允许浮动的元素。取值： left,right,both,none,inherit
9. caption,表格标题显示在表的相对位置，
> caption {caption-side:bottom;}，或者top
10. clip属性，作用在img上时，当图片大于指定区域且position:absolute;时，将按照这里指定属性进行剪裁，如果先有"overflow：visible"，clip属性不起作用:rect(0px,60px,200px,0px)， (top, right, bottom, left);
11. column-count:3将div元素分成指定的列数。
12. filter：img图片的处理，即模糊，黑白等，
属性取值及示例：http://www.runoob.com/cssref/css3-pr-filter.html
13. position指定一个元素（静态的，相对的，绝对或固定）的定位方法的类型，取值：absolute，fixed，relative，static，sticky
14. resize，允许指定的元素区域进行大小拖动调整，取值： none|both|horizontal|vertical:
15. transform:动画转换
16. transition，指定区域的延伸，取值顺序：property duration timing-function delay;
17. display与visibility区别在于，前者隐藏时不占空间，后者即使隐藏也会占用空间
18. 伪元素与伪类
19. Css选择器优先级：总结排序：!important > 行内样式>ID选择器 > 类选择器 > 标签 > 通配符 > 继承 > 浏览器默认属性
20. 脱离文档流：absolute 和 fixed 脱离了文档流，而 static 和 relative 没有脱离文档流。”






