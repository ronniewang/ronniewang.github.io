

# Mixin 是什么

这是一个有多重意思的概念，在不同的领域有不同的解释

在 Ruby 中

先举个例子

在 Less 中，Mixin 指的是一种规则级别的复用

举个例子

``` less

.bordered {
  border-top: dotted 1px black;
  border-bottom: solid 2px black;
}

#menu a {
  color: #111;
  .bordered;
}

.post  a {
  color: red;
  .bordered;
}
```
