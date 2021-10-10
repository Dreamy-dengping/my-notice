## sass的安装

- react vue脚手架当中创建项目就可以配置
- 普通环境下，参考官网的下载方式，vscode中下载插件easy scss就可以自动监视scss文件，然后生成对应的css文件

## 基础语法

- 使用$声明($color:red)

  ```scss
  $myWidth:200px;
  $height:200px;
  ```

- 存在局部作用域

  ```scss
  .box { 
   	 $color:green;//这是在内部声明的变量，外部无法访问，
   	 $width:200px!global;//这是在内部声明的变量，加上!global就可以在外面使用(寓意转化为全局变量)
      
      width: $myWidth;
      height: $height;
      background-color: $color;
      }
  
  .wrap{
      color;$color;//报错
      widht:$widht;//正常
  }
  ```

- {变量}==>模板字符串

  - 避免{}里面的内容被编译(和js里面的模板字符串功能类似)

  ```scss
  $height:200px;
  $font:font;
  .box {
   height: #{$height} / 2;===>编译为  height: 200px / 2;会保留运算符不被直接计算的结果覆盖
      #{$font}-size:20px;
  }
  p:before {
    content: "I ate #{5 + 10} pies!";
  }
  
  p .#{"font"}{
      color:pink	
  }
  ```

- 颜色值计算（不仅仅局限于加）

  ```scss
    background-color: $color1 + $color2;
  ```

- 父级选择器&

  ```
  .box{
  &:hover{
  color:pink;
  }
  }
  ```

- `!default`

  - 起到一个降低优先级的作用，如果之前别人定义过这个变量，那么使用的时候就是用之前别人定义的
  - 如果变量已经被赋值，不会再被重新赋值，但是如果变量还没有被赋值，则会被赋予新的值。（官方的话）

  ```
  $content: "First content";
  $content: "Second content?" !default;
  
  p{
    &::after{ 
      content: $content;   ===》  content: "First content";
    }
  }
  ```

- !global

  - 提升变量作用范围的作用

  ```
  p{
  $color:red ！global;//局部变量    此时就可以全局使用了
  
  }
  a{
  color:$color;
  }
  ```

- 占位符选择器(组件库较多)%

  ```
  #context a%extreme {
    color: blue;
    font-weight: bold;
    font-size: 2em;
  }
  
  .notice {
    @extend %extreme;//最终上面%后面的就会被这个类去替换掉
  }
  
  
  ====》
  #context a.notice {
    color: blue;
    font-weight: bold;
    font-size: 2em;
  }
  
  ```

- ####  `!optional` 

  如果 `@extend` 失败会收到错误提示，比如，这样写 `a.important {@extend .notice}`，当没有 `.notice` 选择器时，将会报错,如果要求 `@extend` 不生成新选择器，可以通过 `!optional` 声明达到这个目的

  ```
  a.important {
    @extend .notice !optional;//如果不加!options就会报错，因为.notice不存在
  }
  ```

## 控制指令

### @if

条件满足就输出

```
p {
  @if 1 + 1 == 2 { border: 1px solid; }
  @if 5 < 3 { border: 2px dotted; }
  @if true  { border: 3px double; }
}

====>
p {
  border: 1px solid;
  border: 3px double;
}


//
$type: monster;
p {
  @if $type == ocean {
    color: blue;
  } @else if $type == matador {
    color: red;
  } @else if $type == monster {
    color: green;
  } @else {
    color: black;
  }
}
p {
  color: green; }

```

###  @for

```
@for $i from 1 through 3 {
  .item-#{$i} { width: 2em * $i; }
}
```



## @import

主要用于文件之间的互相导入

```
@import "foo.scss";   或者 @import "foo";

```

## @extend

继承其他样式，还能自己额外添加自己的样式

```
.error {
  border: 1px #f00;
  background-color: #fdd;
}
.seriousError {
  @extend .error;//继承类名为error的样式
  border-width: 3px;
}
```

## @mixin 与 @include

@mixin 指令允许我们定义一个可以在整个样式表中重复使用的样式。

@include 指令可以将混入（mixin）引入到文档中。

```scss
@mixin important-text {
  color: red;
  font-size: 25px;
  font-weight: bold;
  border: 1px solid blue;
}

selector {
  text-align: center;
  @include important-text;
}
===>
selector {
  text-align: center;
  color: red;
  font-size: 25px;
  font-weight: bold;
  border: 1px solid blue;
}

//带参数传递的
@mixin bordered($color: blue, $width: 1px) {
  border: $width solid $color;
}


.myArticle {
  @include bordered(blue, 1px);  // 调用混入，并传递两个参数
}

.myNotes {
  @include bordered(red, 2px); // 调用混入，并传递两个参数
}


//可变参数
@mixin box-shadow($shadows...) {
      -moz-box-shadow: $shadows;
      -webkit-box-shadow: $shadows;
      box-shadow: $shadows;
}

.shadows {
  @include box-shadow(0px 4px 5px #666, 2px 6px 10px #999);
}


===》
.shadows {
  -moz-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
  -webkit-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
  box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
}


向混合样式中导入内容  @include 后面大括号里面的内容最终会覆盖掉@content

@mixin apply-to-ie6-only {
   html {
    @content;
  }
}
@include apply-to-ie6-only {
  #logo {
    background-image: url(/logo.gif);
  }
  font-size: 12px;
  text-align: center;
  .box{
    color-scheme: pink;
  }
}


```

