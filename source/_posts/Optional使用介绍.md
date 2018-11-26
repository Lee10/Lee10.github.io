---
title: Optional使用介绍
date: 2018-04-03 11:02:36
categories: 
- Java
tags:
- Java8
---

在日常开发中，`NullPointException`一直是困扰我已久的问题，如果只是简单对象的判空还好，只需要`obj != null `就可以解决；但是对象嵌套了多层属性，这个时候需要取里面属性进行判空就很麻烦，代码又长又难看。而Java8中新加的Optional正好可以解决这一问题，让我们在多层属性对象判空时也能写出优雅代码。下面来看看一些用法：<!--more-->
### 构造方法 Optional.of、Optional.ofNullable
`of`、`ofNullable`都接收一个对象入参，区别在于of只接收非null对象，当传入的对象为null时，会抛出`NullPointException`；而ofNullable能null，当入参为null时，为Optional.empty()。

```Java
Optional optional = Optional.of(10L);
Optional optionalNull = Optional.ofNullable(null);
```
### 实例方法
* get  ---获取`Optional`中对象，当对象为null时，抛出`NoSuchElementException`
* isPresent  ---判断`Optional`中对象是否为null，返回为boolean
* ifPresent  ---该方法入参为`Consumer`，若`Optional`有值，则调用`Consumer`；否则不处理
* filter  ---调用该方法会筛选出符合传入条件的实例，若不符合则返回`empty()`
* map  ---若实例有值，则返回传入函数处理后的值，否则返回`empty()`
* flatmap  ---效果与`map`类似，不同的是返回值必须为用`Optional`封装后的实例
* orElse  ---当`Optional`实例为null，返回传入实例
* orElseGet  ---当`Optional`实例为null，返回传入构造方式创建的实例
* orElseThrow  ---当`Optional`实例为null，抛出传入构造方式创建的异常
以下代码片段则为这些实例方法的部分使用场景

```Java
//get
User user = Optional.ofNullable(getById(id)).get();
//isPresent
Optional optional = Optional.ofNullable(getById(id));
if (optional.isPresent()) {
    return optional.get();
}else {
    return new User("tom");
}
//ifPresent
Optional.ofNullable(new User("tom")).ifPresent(user -> System.out.println(user.getName())); //tom
//filter
Optional.ofNullable(new User("tom")).filter(user -> "mini".equals(user.getName()));
//map
String name = Optional.ofNullable(new User("tom")).map(User::getName).get(); //tom
//flatmap
String name = Optional.ofNullable(new User("tom")).flatMap(user -> Optional.ofNullable(user.getName())).get(); //tom
//orElse
System.out.println(Optional.ofNullable(null).orElse(1L)); //1
//orElseGet
User user = (User) Optional.ofNullable(null).orElseGet(User::new);
//orElseThrow
Optional.ofNullable(null).orElseThrow(BusinessException::new); //throw BusinessException
```

### 日常使用
从上面我们已经了解到`Optional`各个实例方法的使用方法，回过头来看篇头提到的多层属性对象的判空问题上，例如我们有以下一段代码。

```Java
User user = getById(id);
if (user != null) {
    Scores scores = user.getScores();
    if (scores != null) {
        System.out.println(scores.getMath());
    }
}
```
可以看到当我们需要使用`User`中的`MenuList`时，需要对`user`、`menuList`、`menu`分别判空，我们如何能够使用`Optional`让代码变得简洁、易懂呢？接下来我们来改写下。

```Java
Optional<User> optional = Optional.ofNullable(getById(id));
optional.map(user -> user.getScores()).map(scores -> scores.getMath()).ifPresent(System.out::println);
```

当然上面展示的只是`Optional`的基本用法，还有很多有趣的用法能够在代码中用到，留给后续慢慢体会吧。







