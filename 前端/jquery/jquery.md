# jquery Note

1.   jquery的语法

     `$(selector).action()`，其中

     -   $符号表示jQuery属性：jquery库在window对象中暴漏两个属性jQuery和$，其中$是jQuery的别名。

     -   selector选择符，选择元素，选择元素的方式有

         -   根据id：$("#myId")

         -   根据类名：$(".myClass")

         -   元素选择器：$("div")

         -   根据属性：$("input[name=someName]")，选择input元素中所有name属性等于someName的属性

         -   使用逗号分隔的选择器列表选择元素

             $("div.myClass ul.people")：选择属于myclass类的div元素，选择属于people的ul元素

     -   action执行对元素的操作

2.   如何确定选择器结果不为空？使用length属性判断

     ```js
     if($("div.foo").length) {
         // todo
     }
     ```

3.   如何过滤元素？

     ```
     $( "div.foo" ).has( "p" );         // div.foo elements that contain <p> tags
     $( "h1" ).not( ".bar" );           // h1 elements that don't have a class of bar
     $( "ul li" ).filter( ".current" ); // unordered list items with class of current
     $( "ul li" ).first();              // just the first unordered list item
     $( "ul li" ).eq( 5 );              // the sixth
     ```

4.   如何选择表单元素？

     -   选择checked的元素：$( "form :checked" );
     -   选择禁用的元素：$( "form :disabled" );
     -   选择启用的元素：$( "form :enabled" );
     -   ......

5.   如何操纵元素

     -   增加元素
     -   删除元素
     -   修改元素

6.   如何增加元素?

     ```js
     // Getting a new element on to the page.
      
     var myNewElement = $( "<p>New element</p>" ); //使用$()增加元素
      
     myNewElement.appendTo( "#content" ); // 增加元素id
      
     myNewElement.insertAfter( "ul:last" ); // This will remove the p from #content!
      
     $( "ul" ).last().after( myNewElement.clone() ); // Clone the p so now we have two.
     
     ```

7.    如何定位元素？

     首先找到一个元素，以此元素为参考点，新增元素。

     ```js
     elemA.insertAfter(elemB); // elemB, elemA
     elemA.after(elemB); // elemA, elemB
     ```

8.   克隆元素

     ```js
     $( "#myList li:first" ).clone().appendTo( "#myList" ); // 
     ```

9.   如何删除元素？
     -   .delete() 会删除与元素绑定的事件
     -   .detach() 不会删除与元素绑定的事件，可以先detach元素，然后再add，所有的东西保持不变。

​     