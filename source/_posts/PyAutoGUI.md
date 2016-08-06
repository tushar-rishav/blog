---
title: PyAutoGUI
tags:
  - Python
  - GUI Automation
date: 2016-05-23 23:27:16
---

Recently, I came across [PyAutoGUI](https://github.com/asweigart/pyautogui), a cross platform **G**raphical **U**ser **I**nterface automation Python module. The module allows us to programmatically control the mouse and keyboard. That means we can write scripts to automate the tasks that involved mouse movements/clicks or inputs from keyboard. To understand better let’s write a simple script that would draw a [Symbol of Peace](https://en.wikipedia.org/wiki/Peace_symbols) for us. If you don’t have any paint tool then you may try online for free at [SumoPaint](http://www.sumopaint.com/).

So before our script executes, we will have Brush tool selected. We could handle the selection of brush tool but it all depends on the position of the brush tool in Paint and it differs for various Paint softwares.
So let’s get started.

Importing required modules. Nothing cool here.
<figure class="highlight python"><table><tr><td class="gutter"><pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
</pre></td><td class="code"><pre><span class="line"><span class="keyword">import</span> math</span>
<span class="line"><span class="keyword">import</span> pyautogui</span>
<span class="line"><span class="keyword">import</span> time</span>
</pre></td></tr></table></figure>

 Ideally, we would want to have control over our automation script even in situations when things go wrong. We could ask script to wait after every function call, giving us a short window to take control of the mouse and keyboard if something goes wrong.
 This pause after each function call can be implemented by setting a certain numeric value to **PAUSE** constant in **pyautogui** module.

<figure class="highlight python"><table><tr><td class="gutter"><pre><span class="line">1</span>
</pre></td><td class="code"><pre><span class="line">pyautogui.PAUSE = <span class="number">1</span>	<span class="comment"># Pause for 1 seconds after every function call</span></span>
</pre></td></tr></table></figure>

We may also want to add an initial delay to let user select an appropriate paint tool.
<figure class="highlight python"><table><tr><td class="gutter"><pre><span class="line">1</span>
</pre></td><td class="code"><pre><span class="line">time.sleep(<span class="number">5</span>)</span>
</pre></td></tr></table></figure>

Screen size can be obtained using `size` method. If you observer carefully, `Symbol of Peace` is a big circle enclosing an inverted `Y`. Circular path can be traced using [parametric equation](https://en.wikipedia.org/wiki/Parametric_equation). Let us assume screen center as circle center.

<figure class="highlight python"><table><tr><td class="gutter"><pre><span class="line">1</span>
<span class="line">2</span>
<span class="line">3</span>
<span class="line">4</span>
<span class="line">5</span>
<span class="line">6</span>
<span class="line">7</span>
<span class="line">8</span>
<span class="line">9</span>
<span class="line">10</span>
</pre></td><td class="code"><pre><span class="line">cx, cy = pyautogui.size()	<span class="comment"># center for circle</span></span>
<span class="line">cx /= <span class="number">2</span></span>
<span class="line">cy /= <span class="number">2</span></span>
<span class="line">radius = <span class="number">100</span>	<span class="comment"># Radius of circle</span></span>
<span class="line">angle = <span class="number">0</span>		<span class="comment"># Frame of reference for revolution.</span></span>
<span class="line">omega = <span class="number">0.3</span>		<span class="comment"># Angular velocity</span></span>
<span class="line"></span>
<span class="line"><span class="comment"># parametric equation of a circle</span></span>
<span class="line">x = cx + radius * math.cos(angle)</span>
<span class="line">y = cy + radius * math.sin(angle)</span>
</pre></td></tr></table></figure>

Mouse clicks can be implemented using **pyautogui.click** method. A mouse click is a combination of the two events:

*   Pressing the button.
*   Releasing the button.

Both combined makes one click. _pyautogui.click_ takes **x** and **y** coordinates of the region to click upon. If these params are not passed then a click is performed at the current mouse position.

Let’s implement mouse click to focus on the paint region.
<figure class="highlight python"><table><tr><td class="gutter"><pre><span class="line">1</span>
</pre></td><td class="code"><pre><span class="line">pyautogui.click(x,y)</span>
</pre></td></tr></table></figure>

Apart from a click, we can also drag mouse cursor. _PyAutoGUI_ provides the **pyautogui.dragTo()** and **pyautogui.dragRel()** functions to drag the mouse cursor to a new location or a location relative to its current one. _dragTo_ takes _x_ and _y_ coordinate of the final position and _dragRel_ takes _x_ and _y_ coordinates and interprets it relative to the current position.
The origin lies at top-left corner of screen and the x-coordinates increase going to the right, and the y-coordinates increase going down. All coordinates are positive integers; there are no negative coordinates.

Now next few lines would create a circular path with enclosed inverted `Y`. The idea is to use parametric equation of circle and keep incrementing the angle until one complete revolution or 2*PI angle has been swept.

<figure class="highlight python"><table><tr><td class="gutter"><pre><span class="line">1</span>
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
</pre></td><td class="code"><pre><span class="line"><span class="keyword">while</span> <span class="number">2</span>*math.pi - angle &gt;= <span class="number">0.001</span>:</span>
<span class="line">	angle = angle + omega</span>
<span class="line">	x = x + radius * omega * math.cos(angle + math.pi / <span class="number">2</span>)</span>
<span class="line">	y = y + radius * omega * math.sin(angle + math.pi / <span class="number">2</span>)</span>
<span class="line">	pyautogui.dragTo(x, y, duration=<span class="number">0.2</span>)</span>
<span class="line"></span>
<span class="line">pyautogui.moveTo(cx, cy-radius, duration=<span class="number">0.2</span>)</span>
<span class="line">pyautogui.dragTo(cx, cy+radius, duration=<span class="number">0.2</span>)</span>
<span class="line">pyautogui.moveTo(cx, cy, duration=<span class="number">0.2</span>)</span>
<span class="line">pyautogui.dragTo(cx - radius * math.cos(math.pi/<span class="number">4</span>), cy + radius * math.sin(math.pi/<span class="number">4</span>), duration=<span class="number">0.2</span>)</span>
<span class="line">pyautogui.moveTo(cx, cy, duration=<span class="number">0.2</span>)</span>
<span class="line">pyautogui.dragTo(cx + radius * math.cos(math.pi/<span class="number">4</span>), cy + radius * math.sin(math.pi/<span class="number">4</span>), duration=<span class="number">0.2</span>)</span>
</pre></td></tr></table></figure>

Combining all together
<figure class="highlight python"><table><tr><td class="gutter"><pre><span class="line">1</span>
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
<span class="line">16</span>
<span class="line">17</span>
<span class="line">18</span>
<span class="line">19</span>
<span class="line">20</span>
<span class="line">21</span>
<span class="line">22</span>
<span class="line">23</span>
<span class="line">24</span>
<span class="line">25</span>
<span class="line">26</span>
<span class="line">27</span>
<span class="line">28</span>
<span class="line">29</span>
<span class="line">30</span>
</pre></td><td class="code"><pre><span class="line"><span class="keyword">import</span> math</span>
<span class="line"><span class="keyword">import</span> pyautogui</span>
<span class="line"><span class="keyword">import</span> time</span>
<span class="line"></span>
<span class="line">time.sleep(<span class="number">5</span>)</span>
<span class="line"></span>
<span class="line">cx, cy = pyautogui.size()</span>
<span class="line">cx /= <span class="number">2</span></span>
<span class="line">cy /= <span class="number">2</span></span>
<span class="line">radius = <span class="number">100</span></span>
<span class="line">angle = <span class="number">0</span></span>
<span class="line">omega = <span class="number">0.3</span></span>
<span class="line"></span>
<span class="line"><span class="comment"># parametric equation of circle</span></span>
<span class="line">x = cx + radius * math.cos(angle)</span>
<span class="line">y = cy + radius * math.sin(angle)</span>
<span class="line">pyautogui.click(x,y)</span>
<span class="line"></span>
<span class="line"><span class="keyword">while</span> <span class="number">2</span>*math.pi - angle &gt;= <span class="number">0.001</span>:</span>
<span class="line">	angle = angle + omega</span>
<span class="line">	x = x + radius * omega * math.cos(angle + math.pi / <span class="number">2</span>)</span>
<span class="line">	y = y + radius * omega * math.sin(angle + math.pi / <span class="number">2</span>)</span>
<span class="line">	pyautogui.dragTo(x, y, duration=<span class="number">0.2</span>)</span>
<span class="line"></span>
<span class="line">pyautogui.moveTo(cx, cy-radius, duration=<span class="number">0.2</span>)</span>
<span class="line">pyautogui.dragTo(cx, cy+radius, duration=<span class="number">0.2</span>)</span>
<span class="line">pyautogui.moveTo(cx, cy, duration=<span class="number">0.2</span>)</span>
<span class="line">pyautogui.dragTo(cx - radius * math.cos(math.pi/<span class="number">4</span>), cy + radius * math.sin(math.pi/<span class="number">4</span>), duration=<span class="number">0.2</span>)</span>
<span class="line">pyautogui.moveTo(cx, cy, duration=<span class="number">0.2</span>)</span>
<span class="line">pyautogui.dragTo(cx + radius * math.cos(math.pi/<span class="number">4</span>), cy + radius * math.sin(math.pi/<span class="number">4</span>), duration=<span class="number">0.2</span>)</span>
</pre></td></tr></table></figure>

You may try and run this script after selecting a brush tool.
Here is a Demo
![Demo](http://res.cloudinary.com/tusharrishav/image/upload/c_scale,w_640/v1464026198/demo_gsvgit.gif)

Please note that this was only a brief introduction to GUI automation using Python. **PyAutoGUI** also provides a bunch of other functions like to perform hotkeys operations (_Ctrl + c_ for copy) etc.
If you find this module interesting you should check out its [documentation.](http://pyautogui.readthedocs.io/en/latest/introduction.html)

Cheers!