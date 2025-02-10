### JS sort使用

比较十进制数字：

.sort(function(a,b){

return a-b;//升序

})



比较字符串：

.sort(function(a,b){

return a.localeCompare(b)

})

把 < 和 > 运算符应用到字符串时，它们只用字符的 Unicode 编码比较字符串，而不考虑当地的排序规则。以这种方法生成的顺序不一定是正确的。localeCompare() 方法提供的比较字符串的方法，考虑了默认的本地排序规则。ECMAscript 标准并没有规定如何进行本地特定的比较操作，它只规定该函数采用底层操作系统提供的排序规则。