---
title: Python f-strings
tags:
  - Python
  - PEP498
date: 2016-07-21 01:07:54
---

Hey there! How are you doing? :)

Since past couple of days I’ve been attending the [EuroPython](https://ep2016.europython.eu/en/) conference at Bilbao, Spain and it has been an _increíble_ experience so far! There are over a dozen amazing talks with something new to share every day and the super fun lightning talks at the end of the day. If for some reason you weren’t able to attend the conference then you may see the talks [live](https://www.youtube.com/results?search_query=europython+2016) at EuroPython YouTube channel.

In this blog I would like to talk briefly about [PEP498](https://mail.python.org/pipermail/python-dev/2015-September/141526.html) - Literal String Interpolation in Python. Python supports multiple ways to format text strings (`%`-formatting, `format` formatting and `Templates`). Each of these are useful in some ways but they do lack in other aspects. For eg. the simplest version of `format` style is too verbose.

<figure class="highlight python"><table><tr><td class="gutter"><pre><span class="line">1</span>
<span class="line">2</span>
</pre></td><td class="code"><pre><span class="line">place = <span class="string">"Bilbao, Spain"</span></span>
<span class="line"><span class="string">"EuroPython is happening at &#123;place&#125;"</span>.format(place=place)</span>
</pre></td></tr></table></figure>

Clearly, there is a redundancy. **place** is being used multiple times. Similarly, `%` formatting is limited with the types (`int`, `str`, `double`) that can be parsed.

**f-strings** are proposed in PEP498\. f-strings are basically a literal strings with ‘f’ or ‘F’ as prefix. It embeds expressions using braces that are evaluated at runtime. Let’s see some simple examples:

<figure class="highlight python"><table><tr><td class="gutter"><pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
<span class="line">6</span>
<span class="line">7</span>
</pre></td><td class="code"><pre><span class="line">place = <span class="string">"Bilbao, Spain"</span></span>
<span class="line">print(f<span class="string">"EuroPython is happening at &#123;place&#125;"</span>)  <span class="comment"># Simple enough, right?</span></span>
<span class="line"></span>
<span class="line"><span class="function"><span class="keyword">def</span> <span class="title">say_hello</span><span class="params">()</span>:</span></span>
<span class="line">    <span class="keyword">return</span> <span class="string">"Hello"</span></span>
<span class="line"></span>
<span class="line">print(f<span class="string">'&#123;say_hello()&#125; there!'</span>)</span>
</pre></td></tr></table></figure>

I think that’s simpler and better than other string formatting options. If this feature interests you and you want to learn more about it then I recommend checking out the PEP498 [documentation](https://www.python.org/dev/peps/pep-0498/).

Cheers!