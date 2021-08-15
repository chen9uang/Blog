# outline和border的区别

### 两者区别：

* Outlines do not take up space
* Outlines may be non-rectangular

outline是不占空间的，outline不一定是方形的。第一点很明显，第二比如在多行的情况，outline会围着内容形成一个不一定是方形的边。例如：

``` html
<div class="box">
  <span>测outine试<span>
</div>
```

``` css
  .box{
    width: 0;
    line-height: 1.4em;
    color: red;
    display: inline-block;
  }
  span{
    fong-size: 20px;
    outline: 1px solid green;
  }
```
结果如图：

![avatar](../images/outline-border.png)
