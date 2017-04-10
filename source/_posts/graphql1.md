---
title: GrapgQL介绍(一)
date: 2017-04-05 22:46:39
tags: graphQL
---

最近在学习[GraphQL](http://www.graphql.org/) API查询语言，想把官网的介绍文档翻译一下，学习的同时分享知识。



> 这一系列的文章是关于学习GraphQL怎么工作以及怎么使用。寻找怎么构建一个GraphQL服务？
> 这里有一些继承于GraphQL的多种语言的库。

<!--more-->

GraphQL是一种针对你的API的查询语言，也是一种通过你对你的数据定义范式执行查询的服务器端运行时。
GraphQL不会绑定特定的数据库或者存储引擎而是使用你当前的代码和数据。


一个GraphQL服务通过定义范式和范式里的字段来创建，然后为范式里的每一个字段提供功能。
例如，下面的GraphQL服务告诉我们谁登陆了(我)以及登陆用户的名称等一些信息：

	type Query {
		me: User
	}

	type User {
		id: ID
		name: String
	}

匹配范式里每一个字段功能：

	function Query_me(request) {
	  return request.auth.user;
	}

	function User_name(user) {
	  return user.getName();
	}

当一个GraphQL服务运行时(通常是一个web服务的一条URL)，它会接收GraphQL查询请求来验证和执行。
接收的查询首先会被检查是不是只关联到定义好的范式和字段，然后运行提供的函数产生结果。

一个查询的例子：

	{
	  me {
		name
	  }
	}

生成的JSON结果：

	{
	  "me": {
		"name": "Luke Skywalker"
	  }
	}
	
学习更多GraphQL -- 查询语言，范式系统，GraphQL服务如何工作以及
使用QraphQL解决问题的最佳实践 -- 请阅读以下章节。

# 查询和修改(Queries and Mutations)

在这个章节，你将详细地学习如何在一个GrapQL服务器上查询。

### 字段(Fields)

最基本的，GraphQL可以请求对象上的指定字段。让我们看一个非常简单的查询以及结果：

	{
	  hero {
		name
	  }
	}
	结果：
	{
	  "data": {
		"hero": {
		  "name": "R2-D2"
		}
	  }
	}

你可以直接地看到查询语句和结果是一样的结构。这是GraphQL最基本的功能，因为服务器明确地知道客户
需要哪些字段，所以你可以得到你预期的结果。

在上面的例子中，字段name作为一个string类型返回，那个名字是星际大战主英雄的名字，`R2-D2`。


> 顺便提一句 - 上面的查询语句是交互式的。也就是你可以按你的意愿改变结构并且得到对应的结果。
> 尝试在`hero`对象的查询语句里增加一个`appearsIn`字段并看看新的结果。

在上个例子中，我们得到了个字符串当我们要求返回hero的name字段，其实返回的字段也可以是对象。
在例子中，你可以为返回的对象设置级联字段*sub-selection*。GraphQL查询可以联通相关的对象以及字段，
让客户端在一个请求中得到所有需要的相关数据，而不是像传统REST架构那样向服务器往返请求多次。

	{
	  hero {
		name
		# Queries can have comments!
		friends {
		  name
		}
	  }
	}
	结果：
	{
	  "data": {
		"hero": {
		  "name": "R2-D2",
		  "friends": [
			{
			  "name": "Luke Skywalker"
			},
			{
			  "name": "Han Solo"
			},
			{
			  "name": "Leia Organa"
			}
		  ]
		}
	  }
	}

注意上面的例子，字段`friends`返回了一个数组。