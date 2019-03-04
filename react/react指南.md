[博文来源](https://juejin.im/post/5b4de4496fb9a04fc226a7af)

# 1、基础搭建 #
1. npm init，配置基础信息，生成package.json
2. 基础包
1、创建工程
> create-react-app lewis-blog

2、服务器启动
> npm start
#指令
	npm run start 启动本地服务  start对应package.json中scripts中的定义
	npm run build 启动编译服务（应用于生产环境）  build对应package.json中scripts中的定义

	
**dependencies**
- yarn add react react-dom

**devDependencies**
yarn add --dev 
	webpack 
	webpack-cli 
	webpack-dev-server 
	babel-core 
	babel-loader 
	babel-polyfill 
	babel-preset-env 
	babel-preset-react 
	babel-preset-stage-0 
	cross-env 
	css-loader 
	file-loader 
	jsx-loader 
	style-loader 
	url-loader
***备注***
	1、webpack4的cli(command line interface)已经移动到webpack-cli了，如果要使用CLI,你需要安装webpack-cli，具体使用可以查看webpack-cli的文档。
	2、由于Babel默认只转换新的JavaScript句法（syntax），而不转换新的API，比如Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise等全局对象，以及一些定义在全局对象上的方法（比如Object.assign），所以如果想使用这些方法就必须使用babel-polyfill，为当前环境提供一个垫片。

	process.env.NODE_ENV === "development"开发环境
	process.env.NODE_ENV === "production"生产环境

# 部署： #
1、本地执行npm run build ，在本地目录生成编译后的文件，比如指定的dist目录
2、将本地生成的代码及静态图片资源传输到服务器，进入该目录（dist），执行:serve -s(必须提前npm i      serve -g)，将显示可以访问的端口及示例访问地址，比如：http://172.19.66.161:5000 ，如果修改端口，例如指定80端口，则执行serve -p 80 -s，
3、在浏览器中输入服务器地址或者IP及对应的端口（5000），就可以访问了!
4、案例1在阿里云centos下的目录：/usr/local/lewis/demos/pro1/dist
5、部署成功并解析可以访问的地址：http://m.lewiscly.com/
![](https://i.imgur.com/ssaZLxq.jpg)

## 插入表格示例 ##
<table><tr><th>Tables</th><th>Are</th><th>Cool</th></tr><tr><td>col 1 is</td><td>left-aligned</td><td>$1600</td></tr><tr><td>col 2 is</td><td>centered</td><td>$12</td></tr><tr><td>col 3 is</td><td>right-aligned</td><td>$1</td></tr></table>


插入代码示例

     <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-test</artifactId>
        </dependency>


//流程图
```flow
st=>start: Start:>https://www.zybuluo.com
io=>inputoutput: verification
op=>operation: Your Operation
cond=>condition: Yes or No?
sub=>subroutine: Your Subroutine
e=>end
st->io->op->cond
cond(yes)->e
cond(no)->sub->io
```

#快捷键
ctrl+alt+i----文件顶部生成提示

#文件创建
1、touch .babelrc 创建babelrc文件

#scss
1、普通变量，使用$定义，$width: 5em，在需要使用的地方使用$调用。
2、全局变量--在定义的变量后方，添加!global标识： $width: 5em !global;
3、嵌套引用，也就是字符串变量插值，即：
	变量$left:left;
	.div1{
		border-#{$left}-width:5px;  //使用#{}引用变量插入到属性名中
	}
4、支持变量计算，即使用普通的运算符及数学公式进行变量运算
5、在嵌套的子层级中，允许使用&引用父元素
.div1{
    &:hover{
        cursor: hand;
    }
}
6、支持属性选择器继承--- @extend .class1;
7、引用外部的css、scss文件,比如通用样式---@import "_test1.scss";
8、Mixin&Include   
定义：
@mixin left {
　　　　float: left;
　　　　margin-left: 10px;
　　}
引用：
div {
　　　　@include left;
　　}
9、参数缺省值（缺省部分赋值）：
    @mixin common($value1,$value2,$defaultValue:12px){
    display:block;
    margin-left:$value1;
    margin-right:$value2;
    padding:$defaultValue;
}
.class1{
    font-size:16px;
    @include common(12px,13px,15px);
}
.class2{
    font-size:16px;
    @include common(12px,13px);
}
10，浏览器前缀设置设置，其实就是属性嵌套和缺省值的联合使用：
定义：
@mixin rounded($vert, $horz, $radius: 10px) {
　　　　border-#{$vert}-#{$horz}-radius: $radius;
　　　　-moz-border-radius-#{$vert}#{$horz}: $radius;
　　　　-webkit-border-#{$vert}-#{$horz}-radius: $radius;
　　}
使用：
　#navbar li { @include rounded(top, left); }
　#footer { @include rounded(top, left, 5px); }
11、编程式方法
scss支持
@if、@else 条件  
@for @i from 1 to length循环,或者@each $member in a, b, c, d {}
@while $i > 0  while
颜色函数：
lighten(#cc3, 10%)  // #d6d65c
darken(#cc3, 10%)  //  #a3a329
grayscale(#cc3) // #808080
complement(#cc3) // #33c