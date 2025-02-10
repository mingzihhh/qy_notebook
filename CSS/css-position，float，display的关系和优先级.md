# css-position，float，display的关系和优先级

1. display：none

   dom元素不产生box, position 和 float 均无效，无生命，无定位

2. position： absolute/fixed

   如果 position 的值是 absolute 或 fixed，，浮动失效，’display’ 会被按规则重置。 

3. float的值不是none

   如果float值不是none，则元素浮动，并且 display 的值 按照 对应表 设置

4. 元素是根元素

   如果元素是根元素，display值按照对应表设置，否则，按照5

5. 应用指定的display特性值

   

   