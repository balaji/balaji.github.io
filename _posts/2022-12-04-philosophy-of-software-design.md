---
layout: post
title:  "Thoughts on A Philosophy of Software Design"
tags: book "software design"
---
![A Philosophy of Software Design](/assets/img/7.jpg)
<p align = "center"><i>A Philosophy of Software Design</i></p>

The book [A Philosophy of Software Design](https://www.amazon.se/dp/173210221X?ref_=cm_sw_r_cp_ud_dp_6Z2G3M186402QX36ZTGM) by John Ousterhout is a short book with easily digestable text.

There are others who did a much better job at reviewing/summarizing the book:
- [http://www.pathsensitive.com/2018/10/book-review-philosophy-of-software.html](http://www.pathsensitive.com/2018/10/book-review-philosophy-of-software.html){:target="_blank"}
- [https://johz.bearblog.dev/book-review-philosophy-software-design/](https://johz.bearblog.dev/book-review-philosophy-software-design/){:target="_blank"}
- [https://lethain.com//notes-philosophy-software-design/](https://lethain.com//notes-philosophy-software-design/){:target="_blank"}
- [https://elvischidera.com/2022-04-29-philosphy-software-design/](https://elvischidera.com/2022-04-29-philosphy-software-design/){:target="_blank"}
- [https://shekhargulati.com/2021/03/17/my-notes-on-the-a-philosophy-of-software-design-book/](https://shekhargulati.com/2021/03/17/my-notes-on-the-a-philosophy-of-software-design-book/){:target="_blank"}
- [https://freshman.tech/philosophy-of-software-design-summary/](https://freshman.tech/philosophy-of-software-design-summary/){:target="_blank"}
- [https://sandstorm.de/de/blog/post/book-review-a-philosophy-of-software-design.html](https://sandstorm.de/de/blog/post/book-review-a-philosophy-of-software-design.html){:target="_blank"}

The author himself summarized his book with an excellent talk at Google: 
<p align="center"><iframe width="560" height="315" src="https://www.youtube.com/embed/bmSAYlu0NcY?controls=0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></p>

My first reaction to the book was that the advices/philosophies were obvious. I didn't feel I learned some ground breaking software design philosophy. But, each philosophy in this book is prone to debate and discourse. The book made these "general wisdom" real with compelling arguments. It felt like listening to a software veteran giving out words of wisdom. The book is aimed towards college undergrads but it will be relevant to developers in all stages of their career. The senior developers will appreciate the refresher, the junior developers will appreciate the learnings without having to make mistakes. The examples and sample codes are worth it.

## Highlights

#### Working code is not enough
Merely having a working code is not good enough. One needs to have the right design mindset to produce maintaninable code in the long term. The author introduces two terms: **Tactical Programming** and **Strategic Programming**. There is also the mention of the programmer who single-handedly takes tactical programming to the extreme: **Tactical Tornado** (love it!). 

A notable mention about this is that the author says that Test Driven Development (TDD) promotes Tactical Programming. TDD when done as a ritual without thinking about the overall design, _does_ promote tactical programming. It is not a silver bullet.

![Tactical vs Strategic](/assets/img/6.jpeg){:width="500px"}
<p><i>Image source: <a href="https://itzone.com.vn/en/article/software-design-p1-it-all-revolves-around-complexity/">
https://itzone.com.vn/en/article/software-design-p1-it-all-revolves-around-complexity/
</a></i></p>

#### Deep module vs Shallow module
> The best modules are deep: they allow a lot of functionalities to be accessed through a simple interface. A shallow module is one with a relatively complex interface, but not much functionality: it doesn't hide much complexity. 

#### Design it twice
> ...consider multiple options for each major design decision: **design it twice**...Try to pick approaches that are radically different from each other; you'll learn more that way. Even if you are certain that there is only one reasonable approach.

#### New layer, new abstraction
> In a well-designed system, each layer provides a different abstraction from the layers above and below it; if you follow a single operation as it moves up and down through layers by invoking methods, the abstractions change with each method call.

#### General purpose classes
> ...the sweet spot is to implement new modules in a _somewhat general-purpose_ fashion...means that the module's functionality should reflect your currrent needs, but its interface should not. Instead, the interface should be general enough to support multiple uses...don't get carried away.

#### Pull complexity downwards

> if the complexity is related to the functionality provided by the module, then...handle the complexity internally with the module...it is more important for a module to have a simple interface than a simple implementation.

There are more good advices. There are mentions about comments, naming, future software trends (which feels like it was written in 2000s), being consistent with design, etc., and the final advice:

#### Decide what matters
> ...good software design is separating what matters from what doesn't matter. Structure software systems around the things matter. For the things that don't matter as much, try to minimize their impact on the rest of the system.

## Bottomline
This is a good reference book - one that I'd want to revisit ever so often.

