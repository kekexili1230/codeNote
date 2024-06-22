# java generics and collections

1.   替换原则

     Substitution Principle: a variable of a given type may be assigned a value of any subtype
     of that type, and a method with a parameter of a given type may be invoked with an
     argument of any subtype of that type.  

     替换原则：给定类型的变量可以被分配该类型的任何子类型的值，并且可以使用该类型的任何子类型的参数来调用具有给定类型参数的方法

     这个原则的意思是，在赋值语句中 A = B，其中A是父类，B可以是子类。

2.   集合相关的子类型

     -   ArrayList<E> is a subtypeof List<E>
     -   List<E> is a subtypeof Collection<E>
     -   List<Integer> is not a subtype of List<Number>

3.   通配符 ? extends E

     -   Collection<? extends E>，这里的? extends E表示里面的元素可以是E的任意子类型
     -   假如A和B是E的子类型，且A和B之间无继承关系，则Collection<A>、Collection<B>、Collection<E>是Collection<? extends E>的子类型。
     -   对于Collection<? extends E>的使用：
         -   作为一个整体：根据替换原则，在变量赋值和函数传参时，可以使用变量Collection<? extends E>接受真实的数据Collection<A>、Collection<B>、Collection<E>
         -   使用其中的元素：只能从Collection<? extends E>类型的变量中get元素

4.   通配符 ? super E

     -   Collection<? super E>，这里的? super E表示里面的元素可以是E的父类型
     -   假如A和B是E的父类型，且A和B之间无继承关系，则Collection<A>、Collection<B>、Collection<E>是Collection<? super E>的子类型。
     -   对于Collection<? super E>的使用：
         -   作为一个整体：根据替换原则，在变量赋值和函数传参时，可以使用变量Collection<? super E>接受真实的数据Collection<A>、Collection<B>、Collection<E>
         -   使用其中的元素：只能向Collection<? super E>类型的变量中put元素，其中元素只能是E的子类型。Collection<? super E>表示里面的元素是E的父类型，但是只能向里面添加E的子类型变量

     