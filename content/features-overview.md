---
title: Features Overview（特性概览）
---

> As an extension to CSS, Less is not only backwards compatible with CSS, but the extra features it adds use existing CSS syntax. This makes learning Less a breeze, and if in doubt, lets you fall back to vanilla CSS.

> 作为CSS的一个扩展，Less不仅仅向后兼容CSS，而且扩展的特性是基于现有的CSS语法的。这使得学习Less变得轻而易举，如果有什么不清楚的，直接退回使用熟悉的CSS即可。

### Variables（变量）

These are pretty self-explanatory:

这些是自解释的代码（很好地说明了Less的特点）：

```less
@nice-blue: #5B83AD;
@light-blue: @nice-blue + #111;

#header {
  color: @light-blue;
}
```

Outputs:

输出：

```css
#header {
  color: #6c94be;
}
```

Note that variables are actually "constants" in that they can only be defined once.

记住这个，Less中的变量本质上类似其他语言的常量，它们只可以被定义一次。

PS：变量在同一个作用域中只可以被定义一次

### Mixins（混合）

Mixins are a way of including ("mixing in") a bunch of properties from one rule-set into another rule-set. So say we have the following class:

mixin是一种把一个规则集合中的大把内容包含到另一个规则集合的方式。假设我们有这样的一个类：

```css
.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}
```

And we want to use these properties inside other rule-sets. Well, we just have to drop in the name of the class where we want the properties, like so:

然后我们想再其他的规则集合中使用bordered类里的内容。很好，我们只需要在想要地方写上这个类的名字即可，就像这样：


```less
#menu a {
  color: #111;
  .bordered;
}

.post a {
  color: red;
  .bordered;
}
```

The properties of the `.bordered` class will now appear in both `#menu a` and `.post a`. (Note that you can also use `#ids` as mixins.)

现在`.bordered`类的内容会同时出现在`#menu a`和`.post a`中。（记住这个，你可以通过类选择器或者ID选择器来使用mixin）

**Learn more（学习更多）**

* [More about mixins](#mixins-feature)
* [Parametric Mixins](#mixins-parametric-feature)


* [更多关于mixins的内容](#mixins-feature)
* [可传参的Mixins](#mixins-parametric-feature)


### Nested Rules（嵌套规则）

Less gives you the ability to use nesting instead of, or in combination with cascading. Let's say we have the following CSS:

Less提供给你使用嵌套规则来代替传统的使用“,”分隔或者不断组合CSS选择器的能力。假设我们有下面这样的CSS代码：

```css
#header {
  color: black;
}
#header .navigation {
  font-size: 12px;
}
#header .logo {
  width: 300px;
}
```

In Less, we can also write it this way:

在Less中，我们也可以用这样的方式写：

```less
#header {
  color: black;
  .navigation {
    font-size: 12px;
  }
  .logo {
    width: 300px;
  }
}
```

The resulting code is more concise, and mimics the structure of your HTML.

产生的代码更简洁，而且很你的HTML代码的结构很相似。

You can also bundle pseudo-selectors with your mixins using this method. Here's the classic clearfix hack, rewritten as a mixin (`&` represents the current selector parent):

在这种方法中，你也可以使用伪类。这是一个经典的清楚浮动的hack，使用mixin重写了（`&`代表当前选择器的父亲）：

PS：在嵌套规则中，当前选择器选择的是嵌套外类的内部元素，`&`代表当前选择器的父亲，也就是外部嵌套外类

```less
.clearfix {
  display: block;
  zoom: 1;

  &:after {
    content: " ";
    display: block;
    font-size: 0;
    height: 0;
    clear: both;
    visibility: hidden;
  }
}
```

**See also（也可以看这里）**

* [Parent Selectors](#parent-selectors-feature)

* [父类选择器](#parent-selectors-feature)



### Nested Directives and Bubbling（嵌套指令与冒泡）

Directives such as `media` or `keyframe` can be nested in the same way as selectors. Directive is placed on top and relative order against other elements inside the same ruleset remains unchanged. This is called bubbling.

Conditional directives e.g. `@Media`, `@supports` and `@document` have also selectors copied into their bodies:
```less
.screen-color {
  @media screen {
    color: green;
    @media (min-width: 768px) {
      color: red;
    }
  }
  @media tv {
    color: black;
  }
}

```
outputs:

```css
@media screen {
  .screen-color {
    color: green;
  }
}
@media screen and (min-width: 768px) {
  .screen-color {
    color: red;
  }
}
@media tv {
  .screen-color {
    color: black;
  }
}
```

Remaining non-conditional directives, for example `font-face` or `keyframes`, are bubbled up too. Their bodies do not change:
```less
#a {
  color: blue;
  @font-face {
    src: made-up-url;
  }
  padding: 2 2 2 2;
}
```

outputs:

```less
#a {
  color: blue;
}
@font-face {
  src: made-up-url;
}
#a {
  padding: 2 2 2 2;
}
```

### Operations

Arithmetical operations `+`, `-`, `*`, `/` can operate on any number, color or variable. If it is possible, mathematical operations take units into account and convert numbers before adding, subtracting or comparing them. The result has leftmost explicitly stated unit type. If the conversion is impossible or not meaningful, units are ignored. Example of impossible conversion: px to cm or rad to %.

```less
// numbers are converted into the same units
@conversion-1: 5cm + 10mm; // result is 6cm
@conversion-2: 2 - 3cm - 5mm; // result is -1.5cm

// conversion is impossible
@incompatible-units: 2 + 5px - 3cm; // result is 4px

// example with variables
@base: 5%;
@filler: @base * 2; // result is 10%
@other: @base + @filler; // result is 15%
```

Multiplication and division do not convert numbers. It would not be meaningful in most cases - a length multiplied by a length gives an area and css does not support specifying areas. Less will operate on numbers as they are and assign explicitly stated unit type to the result.

```less
@base: 2cm * 3mm; // result is 6cm
```

Colors are split into their red, green, blue and alpha dimensions. The operation is applied to each color dimension separately. E.g., if the user added two colors, then the green dimension of the result is equal to sum of green dimensions of input colors. If the user multiplied a color by a number, each color dimension will get multiplied.

Note: arithmetic operation on alpha is not defined, because math operation on colors do not have standard agreed upon meaning. Do not rely on current implemention as it [may change](https://github.com/less/less.js/issues/2694) in later versions.

An operation on colors always produces valid color. If some color dimension of the result ends up being bigger than `ff` or smaller than `00`, the dimension is rounded to either `ff` or `00`. If alpha ends up being bigger than `1.0` or smaller than `0.0`, the alpha is rounded to either `1.0` or `0.0`.

```less
@color: #224488 / 2; //results in #112244
background-color: #112244 + #111; // result is #223355
```

### Escaping

Escaping allows you to use any arbitrary string as property or variable value. Anything inside `~"anything"` or `~'anything'` is used as is with no changes except [interpolation](#variables-feature-variable-interpolation).

```less
.weird-element {
  content: ~"^//* some horrible but needed css hack";
}
```

results in:
```less
.weird-element {
  content: ^//* some horrible but needed css hack;
}
```

### Functions

Less provides a variety of functions which transform colors, manipulate strings and do maths. They are documented fully in the function reference.

Using them is pretty straightforward. The following example uses percentage to convert 0.5 to 50%, increases the saturation of a base color by 5% and then sets the background color to one that is lightened by 25% and spun by 8 degrees:

```less
@base: #f04615;
@width: 0.5;

.class {
  width: percentage(@width); // returns `50%`
  color: saturate(@base, 5%);
  background-color: spin(lighten(@base, 25%), 8);
}
```


### Namespaces and Accessors

(Not to be confused with [CSS `@namespace`](http://www.w3.org/TR/css3-namespace/) or [namespace selectors](http://www.w3.org/TR/css3-selectors/#typenmsp)).

Sometimes, you may want to group your mixins, for organizational purposes, or just to offer some encapsulation. You can do this pretty intuitively in Less, say you want to bundle some mixins and variables under `#bundle`, for later reuse or distributing:

```less
#bundle {
  .button {
    display: block;
    border: 1px solid black;
    background-color: grey;
    &:hover {
      background-color: white
    }
  }
  .tab { ... }
  .citation { ... }
}
```

Now if we want to mixin the `.button` class in our `#header a`, we can do:

```less
#header a {
  color: orange;
  #bundle > .button;
}
```

Note that variables declared within a namespace will be scoped to that namespace only and will not be available outside of the scope via the same syntax that you would use to reference a mixin (`#Namespace > .mixin-name`). So, for example, you can't do the following: (`#Namespace > @this-will-not-work`).

### Scope

Scope in Less is very similar to that of programming languages. Variables and mixins are first looked for locally, and if they aren't found, the compiler will look in the parent scope, and so on.

```less
@var: red;

#page {
  @var: white;
  #header {
    color: @var; // white
  }
}
```

Variables and mixins do not have to be declared before being used so the following Less code is identical to the previous example:

```less
@var: red;

#page {
  #header {
    color: @var; // white
  }
  @var: white;
}
```

**See also**

* [Lazy Loading](#variables-feature-lazy-loading)


### Comments

Both block-style and inline comments may be used:

```less
/* One hell of a block
style comment! */
@var: red;

// Get in line!
@var: white;
```

### Importing

Importing works pretty much as expected. You can import a `.less` file, and all the variables in it will be available. The extension is optionally specified for `.less` files.

```css
@import "library"; // library.less
@import "typo.css";
```
