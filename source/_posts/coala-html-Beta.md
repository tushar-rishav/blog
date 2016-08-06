---
title: coala-html Beta
tags:
  - GSOC
  - coala
  - coala-html
  - Python
date: 2016-05-27 19:03:26
---

So I had been working on coala-html beta version since a few weeks.
The [PR](https://github.com/coala-analyzer/coala-html/pull/46) was certainly huge to be reviewed at once and soon became cumbersome to keep changes updated. But credits to my mentor - [Attila](https://github.com/Uran198) and the constructive feedbacks from [Abdeali](https://github.com/AbdealiJK), I could get it done the right way, making an appropriate and meaningful commits with a better code.

##### [](#What-is-coala-html "What is coala-html?")What is coala-html?

[coala-html](https://github.com/coala-analyzer/coala-html) is a console application that runs [coala](https://github.com/coala-analyzer/coala) analysis and generates an interactive webpage for the user.

##### [](#How-coala-html-works "How coala-html works?")How coala-html works?

coala-html, creates a webpage (Angular app) based on certain json files that are generated - first time when coala-html is run on a given repository, or updated - running coala-html again. By default, the generated webpage is served by launching a server at localhost and the json files are updated. User has an option to change this behaviour by providing the `nolaunch` and `noupdate` arguments respectively  while running the coala-html. User can also provide an optional `dir` or directory path argument that will store the code for the webpage.
You may see a brief demo below:
![Demo](http://res.cloudinary.com/tusharrishav/image/upload/c_scale,w_1080/v1464355604/demo_ipdzsw.gif)

Now as the basic functionalities are done, I am gonna work on improving the UI and writing more tests for having maximum coverage in coming weeks.

Stay tuned! :)