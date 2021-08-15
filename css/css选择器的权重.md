# css选择器的权重

一个选择器的特殊性是根据下列（规则）计算的：

+ 如果声明来自一个'style'属性而不是一条选择器样式规则，算1，否则就是0 (= a)（HTMl中，一个元素的"style"属性值是样式表规则，这些属性没有选择器，所以a=1，b=0，c=0，d=0）
+ 计算选择器中ID属性的数量 (= b)
+ 计算选择器中其它属性和伪类的数量 (= c)
+ 计算选择器中元素名和伪元素的数量 (= d)

``` css
*             {}  /* a=0 b=0 c=0 d=0 -> specificity = 0,0,0,0 */
 li            {}  /* a=0 b=0 c=0 d=1 -> specificity = 0,0,0,1 */
 li:first-line {}  /* a=0 b=0 c=0 d=2 -> specificity = 0,0,0,2 */
 ul li         {}  /* a=0 b=0 c=0 d=2 -> specificity = 0,0,0,2 */
 ul ol+li      {}  /* a=0 b=0 c=0 d=3 -> specificity = 0,0,0,3 */
 h1 + *[rel=up]{}  /* a=0 b=0 c=1 d=1 -> specificity = 0,0,1,1 */
 ul ol li.red  {}  /* a=0 b=0 c=1 d=3 -> specificity = 0,0,1,3 */
 li.red.level  {}  /* a=0 b=0 c=2 d=1 -> specificity = 0,0,2,1 */
 #x34y         {}  /* a=0 b=1 c=0 d=0 -> specificity = 0,1,0,0 */
 style=""          /* a=1 b=0 c=0 d=0 -> specificity = 1,0,0,0 */

```
值得注意的是：通配选择符（universal selector）（*）关系选择符（combinators）（+, >, ~, ' ', ||）和 否定伪类（negation pseudo-class）（:not()）对优先级没有影响。（但是，在 :not() 内部声明的选择器会影响优先级）,css的权重是由上面这些规则决定的，比较特殊的!important优先级比所有的都高。