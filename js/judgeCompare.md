### switch...case && if...else效率比较和优化

> 使用if-else还是switch，最流行的方法是基于测试条件的数量来判断：条件数量越大，越倾向于使用switch而不是if-else。这通常归结于代码的易读性。这个观点认为，当条件较少时，if-else更易读，当条件较多时，使用switch更加易读。--《高性能javascript》

维基百科上`switch_statement`的介绍，关于它的Compilation:

![](https://github.com/dushao103500/js-in-depath/blob/master/images/switch_statement.png)

在编译器中，它会将一系列的语句编译成分支表，然后在判断时，无需像if-else语句一样一个个进行逻辑判断，而是通过在case里的值，对值进行搜索，从而达到对搜索的优化。

![](https://github.com/dushao103500/js-in-depath/blob/master/images/zhihu_switch.png)

> 事实证明，大多数情况下switch比if-else允许得要快，但只有当数量条件很大时，才快得明显。当条件增加的时候，if-else性能负担增加的程度比switch明显得多。因此我们倾向于在条件数量比较少的情况下使用if-else，而在条件数量较大的时候使用switch，出于性能考虑，这是合理的。--《高性能javascript》

> 最小化到达正确分之前所需要判断的条件数量，最简单的方式是将最可能的出现的条件放在首位........if-else语句应该总是按照最大概率到最小概率的顺序排序，或者，最小化条件判断的次数，使用嵌套的方式等。 --《高性能javascript》

一般情况，用if...else的地方一般是判断boolean值，其他情况下，尽量使用switch...case。