# 引言

JavaScript 是一种 ECMAScript（简称ES，是JS的语言规范）方言，在许多程序中得以实现，特别是在网页浏览器。这些方言通常扩展了语言，或者标准库和相关API，例如W3C定义的DOM

> ECMA(European Computer Manufacturers Association) 欧洲计算机制造商协会

JS本身是运行在前端浏览器中的语言，但 Node.js 为 JS 提供了运行时环境，使它可以运行在后端

也可以在任意搭载了 JS 引擎（也可称作JS虚拟机）的设备中执行，比如 V8、SpiderMonkey

TypeScript 由 Microsoft 开发，是 ES 的超集，TS包含了ES的所有特性，同时还扩展了一些新的功能，如静态类型、接口、枚举、泛型等等。TS在编写代码时提供了更好的类型检查和代码提示功能，从而提高了代码的可维护性和可读性。

Angular2.0 和 Vue 3.0 都是基于 TypeScript 的

TypeScript 在运行前需要先编译为 JavaScript，而在编译阶段就会进行类型检查，可以理解为静态类型的 JS
