---
title: "Philosophy Of Software Design"
date: 2022-02-11T19:18:07Z
draft: false
---

This is my short list of ideas I've taken away from reading [a Philosophy of Software Design](https://www.goodreads.com/en/book/show/39996759-a-philosophy-of-software-design) by John Ousterhout.

* All the following advice is to be taken in moderation and with consideration for individual circumstance. Taking anything to it's logical extreme usually results in poor outcomes.
* Complexity is the enemy of understanding.
* 'Deep' interfaces are better than 'shallow' ones. Practically this means: keep your interfaces as simple as possible, and contain/handle the complexity in the implementation. Prime example was the linux/unix file system API.
* Spend time on design. Consider alternatives, discuss them with colleagues.
* Choose accurate, consistent names.
* If a reviewer doesn't understand your code, it is not clear enough. Listen to them, and make it clearer somehow.

I agreed very strongly with all of the above. The idea of 'deep' interfaces expresses something I have been previously unable to express succinctly, and I will try to apply this in my own code and reviews. I also think I will try this tip:

* Write comments/documentation _first_, i.e. while you are designing. This helps to find high-level problems in the design, before you have already spent time coding it.

I disagreed on some minor points:

* in section 8.2, it is advised to avoid configuration parameters. They are an indication that your system isn't handling complexity, and shifting responsibility to the operators of the system. In general, I think it's much better to: 1. make everything configurable (within reason) and 2. provide sensible defaults for the most common cases. The author recommends auto-tuning parameters, however I think this is overkill in most cases, and also takes away control from the operator or user of the software.
* In section 18.2, it is advised to always use the same type for variable declaration and allocation. The author gives the example of a local variable declared as a `java.util.List`, but instanciated as an `ArrayList`, and cites differing performance characteristics as a potential hazard for users of the variable. I think this example is poor; it sounds like premature optimization, and it risks users of the variable using implementation-specific methods and making it more difficult to e.g. change the type of the list later.
* In section 19.3 the author asserts that unit tests have the greatest coverage, and are therefore the best tool for uncovering bugs. I think this is something where YMMV. It really depends on the quality of the unit tests, and the most typical sources of bugs in your application. I think (although I can't prove) that _most_ bugs are found in the interactions of multiple modules, and unit tests will not catch those as they are (by definition) only testing one module.

I would recommend this book to pretty much all software developers at any level. I think everyone in software could find something to take away and use in their work, to the benefit of their projects (and colleagues!).
