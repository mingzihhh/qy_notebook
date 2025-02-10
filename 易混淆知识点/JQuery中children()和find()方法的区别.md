### JQuery中children()和find()方法的区别

1. children及find方法都用是用来获得element的子elements的，两者都不会返回 text node，就像大多数的jQuery方法一样。 
2. children方法获得的仅仅是元素一下级的子元素，即：immediate children。 
3. find方法获得所有下级元素，即：descendants of these elements in the DOM tree 
4. children方法的参数selector 是可选的（optionally），用来过滤子元素，但find方法的参数selector方法是必选的。 
5. find方法事实上可以通过使用 jQuery( selector, context )来实现：英语如是说：Selector context is implemented with the .find() method; therefore, $('li.item-ii').find('li') is equivalent to $('li', 'li.item-ii'). 