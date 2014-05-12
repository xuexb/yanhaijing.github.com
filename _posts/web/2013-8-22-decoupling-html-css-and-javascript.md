---
layout: post
title: 解耦你的html,css和javascript
category : web
tagline: "译"
tags : [web, html, css, javascript]
keywords: [web, html, css, javascript]
description: 今天在web上任何大一点的网站或应用程序都包含大量的html，css和javascript。随着互联网的发展和我们对互联网越来越依赖，计划组织和维护你的前端代码是绝对必要的。
---
{% include JB/setup %}

今天在web上任何大一点的网站或应用程序都包含大量的html，css和javascript。随着互联网的发展和我们对互联网越来越依赖，计划组织和维护你的前端代码是绝对必要的。

今天大一点的web公司，越来越多的人接触越到比例越来越大的前端代码。因此,大多数公司努力保持他们的代码模块化。改变一个应用程序的一部分经常无意中影响到看似不相关的其他部分。

防止意外的影响不是一个容易解决的问题,特别是由于HTML、CSS和JavaScript本身是相互关联的。

更糟糕的是,传统的计算机科学原则如分离关注点,一直是服务器端开发的一部分,却很少被讨论在前端代码中。

在本文中,我将谈谈我是如何学会分离我的HTML、CSS和JavaScript。据我的经验和我所知道的其他人的经验,完成目标的最好的方式是不明显,反直觉的,有时不利于大量所谓的“最佳实践”的。

## 目标 ##

HTML、CSS和JavaScript之间总是会有一些耦合。不管怎样,这些技术被用来互相作用。举个例子,一个fly-in transition(飞入变换)可能在样式表中定义了一个类选择器,但它通常是由用户交互来触发,通过JavaScript来执行，并且通过在html中添加一个类来初始化。

由于在你的前端代码中存在一些耦合是不可避免的。你的目标不应该是简单地完全消除耦合，而应该是最小化耦合,使这部分代码不必依赖其他部分。

后端开发人员应该能够改变HTML模板中的标记而不用担心不小心破坏一个CSS规则或一些JavaScript功能。随着今天的Web团队规模的增大和专业化,这一目标是比以往更重要。

## 反模式 ##

前端代码是不是紧密耦合并不是明显的。然而雪上加霜的是,从一个角度看似松散耦合从另一个角度看事实上却是紧密耦合的。

以下都是反模式，包括我多次见过或我亲自做过并从我的错误中学会的。对每一个,我试图解释为什么耦合是不好的,以及如何避免。

## 过于复杂的选择器 ##

CSS禅意花园向世界证明了你可以完全改变整个网站的外观并丝毫没有改变标记。这是语义Web的运动的海报,和它的一个主要原则是避免使用表象的类。

乍一看,CSS禅意花园可能看起来像一个解耦的很好的例子。毕竟,它的全部意义是将样式从标记分离。然而,问题是,要做到这一点,你通常需要在你的样式表看起来像下面这样的选择器:

	#sidebar section:first-child h3 + p { }

CSS禅意花园中,HTML几乎完全脱离了CSS,但CSS对html是超级耦合和需要结构的标记的知识非常了解。

这看上去还不错如果一个人即维护CSS还维护HTML,但是一旦你增加一些更多的人到你的组合,它很快就会失控。如果一个开发者来临并添加一个&#60;div&#62;在&#60;section&#62;之前,上述规则将不会工作,开发人员可能不知道自己做了什么。

CSS禅意花园是一个伟大的想法,只要你的网站的标记很少改变。但今天的Web应用程序通常不会有这样的情形。

代替冗长,复杂的CSS选择器,最好(只要有可能)风格是你所有的可视化组件有一个或多个类在根元素组件自身上。例如,如果你在你的边栏里有一个子菜单,仅仅添加submenu类到每一个子菜单元素,而不要像下面这样:

	ul.sidebar > li > ul {
	  /* submenu styles */
	}
这种方法最终需要更多的类在HTML中,但它会降低(html,css)之间的耦合,从长远来看这使CSS代码更可重用和可维护。它也可以让你的标记标记标记自我(self-documenting)。如果在HTML中没有类,开发人员不清楚CSS无法预知她的标记变化将影响其他的代码。另一方面,如果在HTML有类那很明显元素被应用的样式和功能。

## 一个类有超过一个职责 ##

有时一个类即用于样式的目的,还作为一个JavaScript钩子(javascript选择符)。虽然这可能看起来像一个节约(因为它需要一个更少的类标记)实际上一个元素表示和行为的耦合。

	<button class="add-item">Add to Cart</button>
上面的例子展示了一个“添加到购物车”按钮样式与添加物品类。

如果开发者想要给这个元素添加一个单击事件监听器,它可以很容易将已经存在的类当作挂钩。我的意思是,如果一个(类)已经存在,为什么要添加另一个呢?

但是想象一下,在整个网站里，有很多这些按钮,他们全都看起来一样,并且全部调用相同的JavaScript功能。然后想象一下营销团队想要其中一个按钮看起来完全与众不同。也许它需要很多更大的更吸引眼球的颜色。

这里有一个问题,因为JavaScript代码里单击事件监听器期待 add-item 类继续被使用,但是你的新按钮现在不能使用这个类(或它必须复原一切已经声明的(样式)并且重置新样式)。更大的问题是,如果你有一些测试代码,也期待着add-item类出现,所以你也会有另一个地方的代码(测试代码)需要更新。

更糟糕的是如果你的 "Add to Cart" 功能使用的不仅仅是这个应用程序。如果代码已经被抽象到一个独立的模块然后一个简单的风格改变现在可能在完全不同的应用程序间引入bug。

使用类作为JavaScript钩子是完全可以接受的(实际上鼓励),但是如果你要这样做,要按这种方法，避免样式类和行为类之间的耦合。

我个人的建议是为所有JavaScript钩子使用前缀。我使用 js - &#42;。这样,当一个开发人员在HTML源码中看到这样一个类(带前缀),她就清楚知道到哪里去找使用这个类的目的。

上面的例子中 "Add to Cart" 将被重写为:

	<button class="js-add-to-cart add-item">Add to Cart</button>

现在,如果一个特定的 "Add to Cart"按钮需要看起来不同,您可以简单地改变样式类和分离行为类本身。

	<button class="js-add-to-cart add-item-special">Add to Cart</button>

## JavaScript对样式知道的太多 ##

同样,JavaScript可以使用类来查找DOM中的元素,它也可以添加或删除类来改变元素的样式。但这可能产生一个问题,如果这些类不是明显区别于出现在页面加载中的类。

如果JavaScript对关于组件样式代码知道的太多,下面的情况将变得非常平常，一个CSS开发人员修改样式表并没有意识到他正在破坏关键的功能。

这并不是说JavaScript不应该更改组件的外观在用户与他们交互后,但应该通过一种一致的接口。它应该使用与定义的默认样式类有明显区别的类。

类似于 js-&#42; 前缀的类,我建议使用 is-&#42; 前缀定义改变可视化组件状态的类选择器。一个CSS规则示例可能看起来像这样:

	.pop-up.is-visible { }

注意,状态类(is-visible)和组件类(pop-up)是交集关系，这一点是重要的。因为状态规则描述组件的状态,他们不应该独立出现。这区别有助于进一步区分状态样式是自默认组件样式的特殊情况。

此外,为了使用一个类似 is-&#42; 的前缀，我们可以编写测试程序来确保我们遵循了规则。一个测试这些类型的规则方法是使用 CSSLint 和 HTML Inspector。

更多关于特定状态类的信息中可以在优秀的书 [SMACSSJonathan Snook](http://snook.ca/) 中找到。

## JavaScript "选择符" ##

jQuery 和新的APIs 如document.querySelectorAll使用户非常容易在DOM中通过语言他们熟悉的CSS选择器查找元素。虽然这是非常强大的,但它有已经CSS选择器出现的同样的问题。

JavaScript“选择器”不应该过度依赖于DOM结构。这样的选择器是非常慢的,而且需要非常了解HTML知识。

参考第一个示例,开发人员在一个HTML模板上工作应该能够对标记做基本更改而不用担心会基本功能产生影像。如果功能是可以打破的,它应该在标记能明显看出。

我已经提到过 js-&#42; 前缀的类应该作为JavaScript钩子。除了将样式类从功能类分离,他们还表达标记的意图。当有人在编辑HTML是看到一个 js-&#42;前缀的类,他们就会知道这是用来做什么的。如果JavaScript代码查询元素不依赖特定的标记结构,在标记中不是明显的什么功能正在发生。

代替长DOM结构复杂的选择器,坚持单类选择器或id选择器。

考虑下面的代码：

	var saveBtn = document.querySelector("#modal div:last-child > button:last-child")

这长的选择器使您不必给HTML添加一个类,也会让你的代码很容易受标记的变化的影像。如果设计师突然决定把保存按钮和取消按钮交换位置,上面的选择器将不再匹配。

一个更好的方法(使用上面提到的前缀的方法)仅仅是使用一个类。

	var saveBtn = document.querySelector(".js-save-btn")

现在的标记可以随意改变,只要类一直在正确的元素上,一切都将很好地工作。

## 类是你的钥匙 ##

几乎所有类型的HTML、CSS和JavaScript之间的耦合可以被减轻通过适当的使用的类和一个可预测的类命名约定。

乍一看在标记中使用大量的类这可能看起来像一个高耦合的信号,因为HTML需要知道这些类的名字。但我认为使用类的做法非常类似传统的程序设计事件模型(隐式风格调用)或外观模式(观察者模式或发布订阅模式)。

在事件驱动编程中,取而代之对象 a 调用对象 b 一个方法,对象 a 只是简单的在一个给定的情况会下会触发特定的事件，而对象b可以订阅该事件。这种方式,对象a不需要知道任何关于对象b 的接口,只需要知道要监听的事件。

可以说事件系统存在一个形式的耦合由于对象B需要知道事件的名字从而订阅,但和对象a需要知道对象B的公共方法相比它是相当松散的耦合。

给HTML添加类是非常相似的。取而代之在CSS文件定义复杂的选择器(这相当于知道HTML的内部接口),它可以通过一个类简单地定义组件的视觉外观。HTML可能会选择使用或不使用这个类，CSS无需关心。

同样,JavaScript不需要复杂DOM序列功能,这也需要非常了解html结构知识,它可以简单的监听用户的交互元素,通过协商的类名。

类应该是将你的HTML、CSS和JavaScript连接在一起的纽带。在我的经验中这是是最简单和最棒的方法来连接这三个技术而没有将他们混合在一起太多。

## 未来 ##

[WHATWG](http://www.whatwg.org/)目前正在为[Web组件](http://www.w3.org/TR/2013/WD-components-intro-20130606/)规范工作,这将允许开发人员将HTML、CSS和JavaScript压在一起作为单独的组件或模块从而从其余的页面封装。

当有一天规范在大多数浏览器中实现,我在这篇文章中给的很多建议将变得不那么关键(因为它会更加明显代码是为了什么工作);然而,它仍然是重要的,理解更广泛的原则和为什么需要他们。

即使在Web组件的时代这些做法变得不那么重要,这一理论仍然适用。用于大型的团队和大型应用程序的做法,仍然会工作对于个体开发人员写的小模块。相反是不必要的情况。

## 结论 ##

编写可维护的HTML、CSS和JavaScript的要点是个体开发人员可以很容易地和自信地编辑部分的代码库而这些变化不经意间影响其他不相关的部分。

防止意想不到的后果的一个最好的方法来是通过一组可预见的类来连接这三个技术,任何开发人员遇到这些类都能清楚它们的意图和选择的他们目的。

为了避免上面的反模式,牢记下面的原则:

+ 在CSS和JavaScript用明确组件和行为类代替复杂的CSS选择器。
+ 风格组件依赖于它们是什么,而不是他们的位置。
+ 样式和行为不要使用相同的类。
+ 从默认的样式分离出状态样式。

这种方式往往在HTML中呈现大量类,但在可预见性和可维护性上的收益是值得的。毕竟,在HTML中添加类是相当容易,并大多数开发人员的技术水平可以做到。引用 [Nicolas Gallagher的一段话](http://nicolasgallagher.com/about-html-semantics-front-end-architecture/):

>当你选择修改HTML和CSS,试图减少你花在写作和编辑CSS上的大量时间,这包括接受,你必须花更多的时间更改HTML元素的类，如果你想改变元素的风格。这被证明是相当实用的,包括前端和后端开发人员——任何人都可以重新排列预先构建的“乐高积木”;事实证明,没有人可以执行css炼金术。

注：本文为翻译文章，原文<[Decoupling Your HTML, CSS, and JavaScript](http://philipwalton.com/articles/decoupling-html-css-and-javascript/)>