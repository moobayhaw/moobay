---
layout: post
title: "使用INDIRECT函数引用Excel单元格"
date: 2012-11-09 23:58
comments: true
categories: 
---

如果需要动态引用Excel单元格的时候，INDIRECT函数可以帮上大忙，为什么这么说？它的传参是一个字符串，比如引用第一列第二行的数据可以用<code>=INDIRECT("A2")</code>，这样有什么好处呢？作比较复杂的数据操作的时候行列可能都是动态计算出来的，这个时候结合<code>ADDRESS(row, column)</code>方法算出行列对应的地址名称传参到INDIRECT（也即<code>=INDIRECT(ADDRESS(row, column))</code>）就可以很方便地引用任意单元格。  

有时候为了方便可能会引用其他表单的数值，假设另外一个表单的名称叫**sheet**，那么引用另外一个表单的单元格可以使用<code>=INDIRECT("sheet!A2")</code>来引用**sheet**表单的第二行第一列的单元格。
