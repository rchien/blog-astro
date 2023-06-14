---
title: Javascript for CSharp Developer
author: Richard Chien
pubDatetime: 2016-02-16
postSlug: javascript-for-experienced-csharp-dev
featured: false
draft: false
tags:
  - c#
  - javascript
description: "Javascript for experienced C# Developer"
---

# Prelude

I’ve been a back-end heavy developer for most of my career. My main tool of choice is C#. For some reason I never got to learn Javascript properly. Also in the same camp are Html and Css. I always learn just know enough to get stuff done. Unfortunately, hacking my way through technologies has never been my strength. I am much more productive when I thoroughly understand something. Besides, I know people at work that can do it 10 times faster hacking together a proof-of-concept. It’s about time for me to bite the bullet and learn JS well.

A good way to learn new stuff is to build on top of existing knowledge or find a way to relate the new to the old. I’ll try to relate to C# concepts as much as I can.

# Js in different env

## JS the assembly language of the web

It’s been echoed many times but I think [Scott Hansleman says it best](http://www.hanselman.com/blog/JavaScriptIsAssemblyLanguageForTheWebSematicMarkupIsDeadCleanVsMachinecodedHTML.aspx). The important point is that JS runs in many environments. From [server](https://nodejs.org/), [desktop](https://github.com/atom/electron), [mobile](https://facebook.github.io/react-native/), and browser. While the core JS is the same, each platform has its own APIs and idiomatic ways of say importing a module.

For example, in browser you’ll be dealing with DOM a lot. So much that at times you’ll think it’s part of JS except it’s not. It’s actually part of browser (aka host environment). In Node you’ll be dealing with Node’s API, and of course React components in React native.

# Aims

This post is meant to kick off a series of JS posts to come. I’ll share my journey to master JS as I go.

The aim of this series is to summarize the essential concepts and tips that may be most useful to C# developers learning JS. There’ll also be a reference section listing useful resources.

Key concepts overview Js is duck typed (but typed nonetheless), JIT compiled language (JIT AFAIK in v8, Chakra and other modern engines). The language syntax doesn’t feel very foreign to experienced C# developer but in the familiarity lies the subtle and nuanced difference that makes me go nuts.

### Before we get started

Best REPL Open browser tab, type about:blank in address bar, open F12 and start typing. To type multiple line into the console at once, use `<shift> + <enter>`.

Search [YDKJS Github repo](https://github.com/getify/You-Dont-Know-JS) to learn or re-learn concepts. THE best free JS resource online AFAIK. For full-featured REPL, use online IDE like [jsfiddle](http://jsfiddle.net/), [CodePen](http://codepen.io/)

Always declare the variable by name before you use it. This prevents ReferenceError: If used on RHS (aka variable lookup) Global scope pollution: If used on LHS (aka variable assignment) JS tries to be helpful and automatically creates variable in global scope (non-strict mode only). `var a = 123; … Console.writeLine(a);`

When you declare a variable, it’s available anywhere in that scope, as well as any lower/inner scope. If the same variable name is declared again in inner scope, the one in inner scope “shadows” (hides) the one in outer scope. ReferenceError is when variable lookup fails following scope lookup rule. JS is a functional language. We can pass function around as values. Think of lambda, delegate, or Func in C#.

```jsx
IIFE (Immediately Invoked Function Expressions)
(function iife() {
  Console.log(”hi”);
})();
```

Use IIFE to create scope and isolate variables without impacting its surroundings

```jsx
Var a = 123

(function iife() {
  Var a = 321;
})();

Console.log(a); //123
```

This is bound dynamically depending on how the function is invoked. There are 4 rules that govern this binding. For a dedicated post to this, prototype, and JS inheritance in general, see future post String literals can be surrounded by `“` or `'`. It’s a stylistic preference. Two ways to reference property in objects. Dot notation: obj.prop1 Bracket notation: `Obj[”prop1”]`. Useful when the property has special characters. For example `obj[”special prop !”]`. Or when the name of the property we need to access is in a variable. `var name = “prop1”; obj[name];`. Note that with bracket notation, the variable in the bracket is coerced to string type. Functions are a subtype of objects. `typeof` returns “function” Javascript has only function scope no block scope. Concretely, `{}` doesn’t create a scope, only function() { // isolated }. For concepts such as variable hoisting, function scope, closure, see coming post

```
- for iterates over properties
- type coersion
- { needs to be at end of line
- no real way of hiding variables. Convention is to use “_variable”
- No const or readonly. Convention is all caps. I.e. “TAX_RATE”
- object can be null or undefined. They are different.
- Fake inheritance, concept of constructor is bolted on. The convention is to use capital for the function.
- Not one way to import packages - Module, AMD, etc.
- Non-strict mode automatically creates variables in global scope
```

Closing To my surprise there aren’t a lot of resources online about Javascript for C# developers. Perhaps most developers went straight to [Typescript](http://www.typescriptlang.org/)?

What has been your experience learning JS?

# Reference

- [Learn Javascript in 2016](https://medium.com/@_cmdv_/i-want-to-learn-javascript-in-2015-e96cd85ad225#.ig14a23bg)
- [Javascript the good parts](http://shop.oreilly.com/product/9780596517748.do)
- [You Don’t Know JS series](https://github.com/getify/You-Dont-Know-JS)
