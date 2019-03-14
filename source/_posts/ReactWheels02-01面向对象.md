---
title: ReactWheels02_01面向对象
date: 2019-03-11 22:58:40
tags: ReactWheels
---

### 面向对象

#### OOP的历史

面向对象是一种编程范式（方法论，思考方法等）

> 历史

- 1950
    ```
    objects和 oriented等技术名词在 MIT里出现，概念尚未明确
    ALGOL 语言里有一些面向对象的早期形态，如消息/方法/成员函数等
    ```
- 1960
    ```
    Simula 语言初步实现面向对象思想，一些重要的概念得以实现，如类/对象/继承/动态绑定等。
    不过 Simula 没实现 private 和 public 等访问修饰符
    ```
- 1970
    ```
    Smalltalk 出现。 它主要是受 Simula启发。
    Lisp 又受到 Smalltalk 影响，开始接纳面向对象思想，最终导致 Flavors (首个引入 mixin 的语言)/ CommonLoops(支持多继承)，这两门语言导致了 Common Lisp 对象系统的出现
    ```
- 1980
    ```
    1981 Adele Golderg 携文将 Smalltalk 和面向对象编程介绍给更多的人。
    1986 第一届 面向对象编程峰会 举办 吸引了1000多人
    期间 c++ 和 Object C 受此影响诞生
    ```
- 1990
    ```
    面向对象思想被广泛采纳，甚至成为主流编程思想，尤其是用户界面编程领域。
    事件驱动编程在它带领下也开始流行(如nodejs)。
    一些不支持面向对象的语言开始慌了 BASIC/Pascal 
    一些从一开始就支持面向对象，也支持以前过程式编程的语言被发明出来如 python ruby
    最重要的商业化面向对象语言是 sun发明的 java 以及微软发明的 c#, VB .net
    ```


> #### 面向对象和函数式不是对立的
