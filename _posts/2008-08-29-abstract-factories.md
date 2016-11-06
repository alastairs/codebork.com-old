---
layout: post
title: Abstract Factories
author: alastairs
nid: 38
created: 1220047740
---
I've come rather late to the <a href="http://en.wikipedia.org/wiki/Design_Patterns" title="Wikipedia entry on Design Patterns">Design Patterns</a> ball; unfortunately it wasn't something that got taught at Warwick (although that may have changed with their new CS course), and so it wasn't until my final year that I even <em>heard</em> of these patterns.  

I am slightly embarrassed to admit this, because they're a pretty fundamental part of programming (or, at least, programming in a <abbr title="Don't Repeat Yourself">DRY</abbr> manner) and the CS education I received from Warwick was in most other respects first class; it's just that they didn't really cover much in terms of "real world" programming techniques.  The emphasis was (rightly, in my opinion) always on teaching the fundamentals and the underlying theories, rather than teaching us how to code Java.  For example, we didn't get any suggestions on good UI design and implementation, but we did get a course on Human-Computer Interaction which was much more wide-ranging and interesting (gotta love Psychology :-).  

Design Patterns provide ready-made solutions to a number of standard programming problems, and in this first post in a <a href="http://www.alastairsmith.me.uk/category/coding/design-patterns" title="Design Patterns' series">series of as-yet-unknown length</a> I will investigate the Abstract Factory pattern.
<!--break-->
I'm posting about the <a href="http://en.wikipedia.org/wiki/Abstract_Factory" title="Wikipedia article on the Abstract Factory pattern">Abstract Factory pattern</a> as I have recently found a need to make use of it!  I realised that the plug-in architecture in my <a href="http://trac.alastairsmith.me.uk/FinanceProg" title="FinanceProg">personal finance program</a> was creating a new instance of a plug-in each time a data object was created.  So to create a transaction, for example, a new instance of the Transaction plug-in was created, complete with all the metadata for the plug-in, such as its author and version, etc.  This seemed sub-optimal, and was causing problems with new Transactions not being initialised before use, so I decided to split the data objects from their plug-ins.  Suddenly I needed a way to be able to ask a plug-in for a new instance of a data object.  "Great," I thought to myself, "this sounds like a job for the Factory pattern".  After a bit of digging around Wikipedia's articles on creational patterns, I came across the Abstract Factory pattern, and found that this did exactly what I wanted: it creates new objects of the same general type (e.g., "an account"), but with different specifics (such as "a credit card account" or "a current account").  

The pattern goes a little something like this:  

<blockquote>A factory makes products.  My design for a factory specifies that it makes buttons.  They might be square buttons, round buttons, red buttons or blue buttons, but precisely which type doesn't matter, as they're all buttons.  It's only once the factory has been built to the specification that it starts making buttons of a particular type, and we can make a particular type of button by finding the appropriate factory and asking it for a button.  Later on, I find I want to be able to make green diamond-shaped buttons too, so I build a new factory to the same blueprint but whose final product is a green diamond-shaped button.  </blockquote>

[img_assist|nid=39|title=|desc=|link=node|align=center|width=400|height=256|title=The Abstract Factory Pattern in UML form]

Note from the description and the diagram that <strong>there are two distinct elements to the pattern: the factory and the product</strong>.  With the Abstract Factory pattern, the detail of both elements has to be abstracted; it doesn't make sense otherwise, and you end up with a regular Factory pattern that's behaving a little strangely.

So we end up with two interfaces: <code>IFactory</code> (<code>GUIFactory</code> in the diagram) and <code>IProduct</code> (<code>Button</code>).  <code>IFactory</code> defines the factory method (<code>createButton</code>) and <code>IProduct</code> the product's characteristics (e.g. <code>paint()</code>).  Each of these are implemented by concrete factories and products respectively, and in the manner that is required for the concrete factories (e.g. to make red round buttons).  The <a href="http://en.wikipedia.org/wiki/Abstract_Factory#Java">Java sample on the Wikipedia page</a> indicates that the abstract factory should define a single static method (<code>getFactory()</code>) to determine the correct factory to use; for example, the decision could be made based upon a configuration file value, or the platform upon which the program is running.  It is this method that distinguishes this pattern as something unique, and not just an implementation of the Factory pattern via the use of interfaces.  Depending on your situation, you could provide an override to <code>getFactory()</code> that specifies a preference for the object type returned.  However, this is mostly in violation of the abstraction inherent in the pattern, and may even be in violation of the pattern itself too.  

Constructors on the concrete products should be specified as <code lang="java">protected</code> in Java, or <code lang="csharp">internal</code> in C#, to prevent direct instantiation. <em>At least one constructor must be defined in order to hide the default constructor</em>, which is <code lang="csharp">public</code> and parameter-less in both Java and C#.  

By far, the greatest advantage of this pattern is that the client does not need to know the detail of the concrete implementation &mdash; an IProduct is an IProduct is an IProduct is an IProduct, to <a href="http://en.wikipedia.org/wiki/A_rose_is_a_rose_is_a_rose_is_a_rose" title="A Rose is a rose is a rose is a rose">paraphrase</a> &mdash; and so the concrete implementations can be passed around via references to their parent interfaces.  This maintains the abstraction and (partially) upholds the principles of design by contract.  These are all Good Things&trade;.

One thing that I'm not certain of in this and the Factory pattern is how a created object should be initialised.  Factory methods seem to be parameter-less by design, which means that the products then have to have their attributes specified later.  This seems a bit like paying for and receiving your new car and <strong>then having to paint it, fit the engine and upholster the interior</strong>.  Equally, however, I don't want to receive the same bog-standard Car as everyone else; I want a 2.0-litre diesel engine, I want leather upholstery, and I want it in <span style="color:#004225; font-weight:bold">British Racing Green</span>.  This would normally be done by passing parameters to the constructor, but this obviously isn't possible with a Factory pattern as the product's constructors aren't available for use; you <em>have to go through the factory</em>.  

The Abstract Factory pattern is a powerful method for creating objects in an abstract environment, such as a plug-in environment.  It also has other uses in, for example, a database abstraction layer and other similar situations where an abstraction for another part of the system should be provided in place of a concrete implementation.