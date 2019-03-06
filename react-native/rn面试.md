**1、当你调用setState的时候，发生了什么事？**
> 1、当调用 setState 时，React会做的第一件事情是将传递给 setState 的对象合并到组件的当前状态。
> 2、这将启动一个称为和解（reconciliation）的过程。
> 3、和解（reconciliation）的最终目标是以最有效的方式，根据这个新的状态来更新UI。
> 4、为此，React将构建一个新的 React 元素树（您可以将其视为 UI 的对象表示）。
> 6、一旦有了这个树，为了弄清 UI 如何响应新的状态而改变，React 会将这个新树与上一个元素树相比较（ diff ）。
> 7、通过这样做， React 将会知道发生的确切变化，并且通过了解发生什么变化，只需在绝对必要的情况下进行更新即可最小化 UI 的占用空间。

**2、React中Element 和 Component 有何区别？**
> 简单地说，一个 React element 描述了你想在屏幕上看到什么。
> 换个说法就是，一个 React element 是一些 UI 的对象表示。
> 一个 React Component 是一个函数或一个类，
> 它可以接受输入并返回一个 React element 
> (通常是通过 JSX ，它被转化成一个 createElement 调用）。

**3、在使用redux过程中，如何防止定义的action-type的常量重复？**
> ES6引入了一种新的原始数据类型Symbol，表示独一无二的值。
> Symbol函数前不能使用new命令，否则会报错。这是因为生成的Symbol是一个原始类型的值，不是对象
> Symbol函数可以接受一个字符串作为参数，表示对Symbol实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。

**4、XSS与CSRF介绍**
> XSS是一种跨站脚本攻击，是属于代码注入的一种，攻击者通过将代码注入网页中，其他用户看到会受到影响(代码内容有请求外部服务器);
> 
> CSRF是一种跨站请求伪造，冒充用户发起请求，完成一些违背用户请求的行为(删帖，改密码，发邮件，发帖等)

5、react-native原理分析
链接1：https://www.jianshu.com/p/038975d7f22d
链接2：https://juejin.im/post/5a6460f8f265da3e4f0a446d