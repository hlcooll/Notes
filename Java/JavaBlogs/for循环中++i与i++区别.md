# for循环中`++i`与`i++`区别


两者在 for 循环中的作用是一模一样的，但是 `++i`消耗的时间比`i++`少。

原因：Java 中的 `i++`语句是需要临时变量取存储返回值自增前的值，而`++i` 不需要。使得使用 `i++`需要先申请一段内存空间，然后将值塞进去，不用了才释放空间。