---
title: TDD and BDD
tags:
  - GSOC
  - coala
  - coala-html
  - Karma
  - Mocha
  - Chai
date: 2016-06-14 20:01:49
---

Past a few days been hectic resulting from the fairly complicated overall Visa application process for Spain (need it for [EuroPython’16](http://gtushar.co/blog/2016/05/22/europython/) ). Meanwhile, I have been reading about TDD or _Test Driven Development_ and BDD or _Behaviour Driven Development_ to write tests for the controllers using [Mocha](https://mochajs.org/) - a testing framework, [Chai](http://chaijs.com/) - a BDD assertion library and [Karma](https://github.com/karma-runner/karma) - a test _runner_. Test runner can run tests based on different test frameworks - in this case. Mocha. Their documentaions might seem bloating at first sight. I felt the same when I started reading about them. But it’s actually simple once you’ve understood the concept behind TDD. Through this blog I shall try to explain why we should follow TDD approach and also write some basic tests using _Mocha_ and _Chai_ together. Let’s drop karma for a while.

### [](#Why-TDD "Why TDD?")Why TDD?

Originally, TDD meant writing tests before the actual implementation. ( But you may write tests afterwards too (not a good approach though). Now, the question comes - how can one write tests for something that haven’t been implemented yet. Makes sense, isn’t it? Well usually when you test modules/functions , you already know your expectations. For instance, If I am going to write a function that checks if a given real number is a power of 2, I already know the outcome. In this case it’s a boolean value - `True` for power of 2 and `False` otherwise. So, you may very well write tests using this information.
There are many advantages of adopting TDD. First being avoiding _Regression bugs_. Regression bug is a bug that had been fixed in the past and then occurred again. For instance, we change obviously unrelated piece of code and therefore we do not check some old problem, because we do not expect that problem to occur again. Once we have an automated test for this bug, it will not happen again because we can easily run all tests instead of manually trying only the parts that are obviously related to the change we made.
Another reason being _Refactoring_. The code architecture may require changes to a project requirements. Tests prove whether the code still works, even after a major refactoring.

### [](#Get-started "Get started")Get started

#### [](#Installation "Installation")Installation

You may install karma using `npm` - node package manager. To install the Karma plugins for Mocha and Chai. Also, it’d be nice to use PhantomJS for headless testing. Let’s create a `pacakge.json` file and add the dependencies to it. It might look like:
<figure class="highlight js"><table><tr><td class="gutter"><pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
<span class="line">6</span>
<span class="line">7</span>
<span class="line">8</span>
<span class="line">9</span>
<span class="line">10</span>
<span class="line">11</span>
<span class="line">12</span>
<span class="line">13</span>
<span class="line">14</span>
<span class="line">15</span>
</pre></td><td class="code"><pre><span class="line">&#123;</span>
<span class="line">  <span class="string">"devDependencies"</span>: &#123;</span>
<span class="line">    <span class="string">"chai"</span>: <span class="string">"^3.5.0"</span>,</span>
<span class="line">    <span class="string">"karma"</span>: <span class="string">"^0.13.21"</span>,</span>
<span class="line">    <span class="string">"karma-chai"</span>: <span class="string">"^0.1.0"</span>,</span>
<span class="line">    <span class="string">"karma-jasmine"</span>: <span class="string">"^1.0.2"</span>,</span>
<span class="line">    <span class="string">"karma-mocha"</span>: <span class="string">"^0.2.2"</span>,</span>
<span class="line">    <span class="string">"mocha"</span>: <span class="string">"^2.4.5"</span>,</span>
<span class="line">    <span class="string">"karma-phantomjs-launcher"</span>: <span class="string">"^1.0.0"</span>,</span>
<span class="line">    <span class="string">"phantomjs-prebuilt"</span>: <span class="string">"^2.1.4"</span></span>
<span class="line">  &#125;,</span>
<span class="line">  <span class="string">"scripts"</span>: &#123;</span>
<span class="line">    <span class="string">"test"</span>: <span class="string">"./node_modules/.bin/karma start"</span></span>
<span class="line">  &#125;</span>
<span class="line">&#125;</span>
</pre></td></tr></table></figure>

Now, run `npm install` to fetch and install the dependencies.
Also, to make it easier to run karma from the command line you can install `karma-cli` globally, which will run the local version without having to specify the path to karma (node node_modules/karma/bin/karma):
<figure class="highlight sh"><table><tr><td class="gutter"><pre><span class="line">1</span>
</pre></td><td class="code"><pre><span class="line">npm install -g karma-cli</span>
</pre></td></tr></table></figure>

Karma needs a [configuration file](https://karma-runner.github.io/0.13/config/configuration-file.html). Create one by running `karma init` and answering the simple question. Make sure you specify `Mocha` as your testing framework. Also, mention source and tests files location accordingly. In my case it’s in `./app/**/*.js` for source and `./tests/**/*Spec.js` for tests.
Let’s create two empty files `app/powerOfTwo.js` and `tests/powerOfTwoSpec.js`. It’d look like

<figure class="highlight css"><table><tr><td class="gutter"><pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
</pre></td><td class="code"><pre><span class="line">├── <span class="selector-tag">app</span></span>
<span class="line">│   └── <span class="selector-tag">powerOfTwo</span><span class="selector-class">.js</span></span>
<span class="line">└── <span class="selector-tag">tests</span></span>
<span class="line">    └── <span class="selector-tag">powerOfTwoSpec</span><span class="selector-class">.js</span></span>
</pre></td></tr></table></figure>

Once you’re done, you have `karma-conf.js`. To get Chai included in the test pipeline, edit karma-conf.js and add it to the frameworks setting. Also to be able to use PhantomJS mention it in `browsers`.
<figure class="highlight js"><table><tr><td class="gutter"><pre><span class="line">1</span>
<span class="line">2</span>
</pre></td><td class="code"><pre><span class="line">frameworks: [<span class="string">'mocha'</span>, <span class="string">'chai'</span>]</span>
<span class="line">browsers: [<span class="string">'PhantomJS'</span>]</span>
</pre></td></tr></table></figure>

Running `karma start` will execute the default karma-conf.js. You can have multiple configuration files which can be run by specifying the name of the configuration file. `karma start &lt;conf-file-name&gt;`.

Enough installation. Let’s get down to writing tests.

#### [](#Writing-tests "Writing tests")Writing tests

By default, you can use Mocha’s assertion module (which is in fact Node’s regular assertion module) to run your tests. However, it can be quite limiting. This is where assertion libraries like Chai enter the frame. Writing a test is like constructing a perfect sentence in English. Don’t believe me? [Hm see yourself](http://chaijs.com/guide/styles/). We _describe_ an umbrella of tests, and state some _expected_ outputs for various tests under that umbrella.

Write this in `tests/powerOfTwoSpec.js`.

<figure class="highlight js"><table><tr><td class="gutter"><pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
<span class="line">6</span>
<span class="line">7</span>
<span class="line">8</span>
<span class="line">9</span>
<span class="line">10</span>
<span class="line">11</span>
<span class="line">12</span>
<span class="line">13</span>
<span class="line">14</span>
<span class="line">15</span>
</pre></td><td class="code"><pre><span class="line">describe(<span class="string">'powerOfTwo'</span>, <span class="function"><span class="keyword">function</span>(<span class="params"></span>)</span>&#123; <span class="comment">// The Umbrella test. You can create as many depending on your code architecture</span></span>
<span class="line"></span>
<span class="line">	<span class="comment">// Individual tests</span></span>
<span class="line">	it(<span class="string">'powerOfTwo should return a boolean value'</span>, <span class="function"><span class="keyword">function</span>(<span class="params"></span>) </span>&#123;</span>
<span class="line">	  powerOfTwo(<span class="number">64</span>).should.be.a(<span class="string">'boolean'</span>);	<span class="comment">// I wish Visa process was as simple as this!!</span></span>
<span class="line">	&#125;);</span>
<span class="line"></span>
<span class="line">	it(<span class="string">'powerOfTwo should return `true` for power of 2'</span>, <span class="function"><span class="keyword">function</span>(<span class="params"></span>)</span>&#123;</span>
<span class="line">	  expect(powerOfTwo(<span class="number">64</span>)).to.be.true;</span>
<span class="line">	&#125;);</span>
<span class="line"></span>
<span class="line">	it(<span class="string">'powerOfTwo should return `false` for non power of 2'</span>, <span class="function"><span class="keyword">function</span>(<span class="params"></span>)</span>&#123;</span>
<span class="line">	  expect(powerOfTwo(<span class="number">31</span>)).to.be.false;</span>
<span class="line">	&#125;);</span>
<span class="line">&#125;);</span>
</pre></td></tr></table></figure>

Note that we haven’t created the `powerOfTwo` function yet. But looking at the tests, we can say how our function is _expected_ to _behave_. That’s TDD and BDD for you in the simplest form.

Now let’s write our powerOfTwo function in `powerOfTwo.js`

<figure class="highlight js"><table><tr><td class="gutter"><pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
</pre></td><td class="code"><pre><span class="line"><span class="keyword">var</span> powerOfTwo = <span class="function"><span class="keyword">function</span>(<span class="params">num</span>) </span>&#123;</span>
<span class="line">	<span class="keyword">return</span> num &amp;&amp; (!(num &amp; (num<span class="number">-1</span>)));</span>
<span class="line">&#125;</span>
</pre></td></tr></table></figure>

Finally, we may run the tests running `npm test` in project root directory.

I hope that was simple and introduced you to the basics of TDD/BDD. Go ahead and try yourself.
Explore more at 

*   [Chai-BDD](http://chaijs.com/api/bdd/)
*   [Mocha](https://mochajs.org/) and*   [Karma](https://karma-runner.github.io/0.13/index.html)

Cheers! :)