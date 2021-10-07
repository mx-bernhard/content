---
title: Drawing graphics
slug: Learn/JavaScript/Client-side_web_APIs/Drawing_graphics
tags:
  - API
  - Article
  - Beginner
  - Canvas
  - CodingScripting
  - Graphics
  - JavaScript
  - Learn
  - WebGL
---
<div>{{LearnSidebar}}{{PreviousMenuNext("Learn/JavaScript/Client-side_web_APIs/Third_party_APIs", "Learn/JavaScript/Client-side_web_APIs/Video_and_audio_APIs", "Learn/JavaScript/Client-side_web_APIs")}}</div>

<p>The browser contains some very powerful graphics programming tools, from the Scalable Vector Graphics (<a href="/en-US/docs/Web/SVG">SVG</a>) language, to APIs for drawing on HTML {{htmlelement("canvas")}} elements, (see <a href="/en-US/docs/Web/API/Canvas_API">The Canvas API</a> and <a href="/en-US/docs/Web/API/WebGL_API">WebGL</a>). This article provides an introduction to canvas, and further resources to allow you to learn more.</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>JavaScript basics (see <a href="/en-US/docs/Learn/JavaScript/First_steps">first steps</a>, <a href="/en-US/docs/Learn/JavaScript/Building_blocks">building blocks</a>, <a href="/en-US/docs/Learn/JavaScript/Objects">JavaScript objects</a>), the <a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction">basics of Client-side APIs</a></td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To learn the basics of drawing on <code>&lt;canvas&gt;</code> elements using JavaScript.</td>
  </tr>
 </tbody>
</table>

<h2 id="Graphics_on_the_Web">Graphics on the Web</h2>

<p>As we talked about in our HTML <a href="/en-US/docs/Learn/HTML/Multimedia_and_embedding">Multimedia and embedding</a> module, the Web was originally just text, which was very boring, so images were introduced — first via the {{htmlelement("img")}} element and later via CSS properties such as {{cssxref("background-image")}}, and <a href="/en-US/docs/Web/SVG">SVG</a>.</p>

<p>This however was still not enough. While you could use <a href="/en-US/docs/Learn/CSS">CSS</a> and <a href="/en-US/docs/Learn/JavaScript">JavaScript</a> to animate (and otherwise manipulate) SVG vector images — as they are represented by markup — there was still no way to do the same for bitmap images, and the tools available were rather limited. The Web still had no way to effectively create animations, games, 3D scenes, and other requirements commonly handled by lower level languages such as C++ or Java.</p>

<p>The situation started to improve when browsers began to support the {{htmlelement("canvas")}} element and associated <a href="/en-US/docs/Web/API/Canvas_API">Canvas API</a> — Apple invented it in around 2004, and other browsers followed by implementing it in the years that followed. As you'll see below, canvas provides many useful tools for creating 2D animations, games, data visualizations, and other types of app, especially when combined with some of the other APIs the web platform provides.</p>

<p>The below example shows a simple 2D canvas-based bouncing balls animation that we originally met in our <a href="/en-US/docs/Learn/JavaScript/Objects/Object_building_practice">Introducing JavaScript objects</a> module:</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/oojs/bouncing-balls/index-finished.html", '100%', 500)}}</p>

<p>Around 2006–2007, Mozilla started work on an experimental 3D canvas implementation. This became <a href="/en-US/docs/Web/API/WebGL_API">WebGL</a>, which gained traction among browser vendors, and was standardized around 2009–2010. WebGL allows you to create real 3D graphics inside your web browser; the below example shows a simple rotating WebGL cube:</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/apis/drawing-graphics/threejs-cube/index.html", '100%', 500)}}</p>

<p>This article will focus mainly on 2D canvas, as raw WebGL code is very complex. We will however show how to use a WebGL library to create a 3D scene more easily, and you can find a tutorial covering raw WebGL elsewhere — see <a href="/en-US/docs/Web/API/WebGL_API/Tutorial/Getting_started_with_WebGL">Getting started with WebGL</a>.</p>

<div class="note">
<p><strong>Note:</strong> Basic canvas functionality is supported well across browsers, with the exception of IE 8 and below for 2D canvas, and IE 11 and below for WebGL.</p>
</div>

<h2 id="Active_learning_Getting_started_with_a_&lt;canvas&gt;">Active learning: Getting started with a &lt;canvas&gt;</h2>

<p>If you want to create a 2D <em>or</em> 3D scene on a web page, you need to start with an HTML {{htmlelement("canvas")}} element. This element is used to define the area on the page into which the image will be drawn. This is as simple as including the element on the page:</p>

<pre class="brush: html">&lt;canvas width="320" height="240"&gt;&lt;/canvas&gt;</pre>

<p>This will create a canvas on the page with a size of 320 by 240 pixels.</p>

<p>Inside the canvas tags, you can put some fallback content, which is shown if the user's browser doesn't support canvas.</p>

<pre class="brush: html">&lt;canvas width="320" height="240"&gt;
  &lt;p&gt;Your browser doesn't support canvas. Boo hoo!&lt;/p&gt;
&lt;/canvas&gt;</pre>

<p>Of course, the above message is really unhelpful! In a real example you'd want to relate the fallback content to the canvas content. For example, if you were rendering a constantly updating graph of stock prices, the fallback content could be a static image of the latest stock graph, with alt text saying what the prices are in text.</p>

<h3 id="Creating_and_sizing_our_canvas">Creating and sizing our canvas</h3>

<p>Let's start by creating our own canvas that we draw future experiments on to.</p>

<ol>
 <li>
  <p>First make a local copy of our <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/0_canvas_start.html">0_canvas_start.html</a> file, and open it in your text editor.</p>
 </li>
 <li>
  <p>Add the following code into it, just below the opening {{htmlelement("body")}} tag:</p>

  <pre class="brush: html">&lt;canvas class="myCanvas"&gt;
  &lt;p&gt;Add suitable fallback here.&lt;/p&gt;
&lt;/canvas&gt;</pre>

  <p>We have added a <code>class</code> to the <code>&lt;canvas&gt;</code> element so it will be easier to select if we have multiple canvases on the page, but we have removed the <code>width</code> and <code>height</code> attributes for now (you could add them back in if you wanted, but we will set them using JavaScript in a below section). Canvases with no explicit width and height default to 300 pixels wide by 150 pixels high.</p>
 </li>
 <li>
  <p>Now add the following lines of JavaScript inside the {{htmlelement("script")}} element:</p>

  <pre class="brush: js">const canvas = document.querySelector('.myCanvas');
const width = canvas.width = window.innerWidth;
const height = canvas.height = window.innerHeight;</pre>

  <p>Here we have stored a reference to the canvas in the <code>canvas</code> constant. In the second line we set both a new constant <code>width</code> and the canvas' <code>width</code> property equal to {{domxref("Window.innerWidth")}} (which gives us the viewport width). In the third line we set both a new constant <code>height</code> and the canvas' <code>height</code> property equal to {{domxref("Window.innerHeight")}} (which gives us the viewport height). So now we have a canvas that fills the entire width and height of the browser window!</p>

  <p>You'll also see that we are chaining assignments together with multiple equals signs — this is allowed in JavaScript, and it is a good technique if you want to make multiple variables all equal to the same value. We wanted to make the canvas width and height easily accessible in the width/height variables, as they are useful values to have available for later (for example, if you want to draw something exactly halfway across the width of the canvas).</p>
 </li>
 <li>
  <p>If you save and load your example in a browser now, you'll see nothing, which is fine, but you'll also see scrollbars — this is a problem for us, which happens because the {{htmlelement("body")}} element has a {{cssxref("margin")}} that, added to our full-window-size canvas, results in a document that's wider than the window. To get rid of the scrollbars, we need to remove the {{cssxref("margin")}} and also set {{cssxref("overflow")}} to <code>hidden</code>. Add the following into the {{htmlelement("head")}} of your document:</p>

  <pre class="brush: html">&lt;style&gt;
  body {
    margin: 0;
    overflow: hidden;
  }
&lt;/style&gt;</pre>

  <p>The scrollbars should now be gone.</p>
 </li>
</ol>

<div class="note">
<p><strong>Note:</strong> You should generally set the size of the image using HTML attributes or DOM properties, as explained above. You could use CSS, but the trouble then is that the sizing is done after the canvas has rendered, and just like any other image (the rendered canvas is just an image), the image could become pixelated/distorted.</p>
</div>

<h3 id="Getting_the_canvas_context_and_final_setup">Getting the canvas context and final setup</h3>

<p>We need to do one final thing before we can consider our canvas template finished. To draw onto the canvas we need to get a special reference to the drawing area called a context. This is done using the {{domxref("HTMLCanvasElement.getContext()")}} method, which for basic usage takes a single string as a parameter representing the type of context you want to retrieve.</p>

<p>In this case we want a 2d canvas, so add the following JavaScript line below the others inside the <code>&lt;script&gt;</code> element:</p>

<pre class="brush: js">const ctx = canvas.getContext('2d');</pre>

<div class="note">
<p><strong>Note:</strong> other context values you could choose include <code>webgl</code> for WebGL, <code>webgl2</code> for WebGL 2, etc., but we won't need those in this article.</p>
</div>

<p>So that's it — our canvas is now primed and ready for drawing on! The <code>ctx</code> variable now contains a {{domxref("CanvasRenderingContext2D")}} object, and all drawing operations on the canvas will involve manipulating this object.</p>

<p>Let's do one last thing before we move on. We'll color the canvas background black to give you a first taste of the canvas API. Add the following lines at the bottom of your JavaScript:</p>

<pre class="brush: js">ctx.fillStyle = 'rgb(0, 0, 0)';
ctx.fillRect(0, 0, width, height);</pre>

<p>Here we are setting a fill color using the canvas' {{domxref("CanvasRenderingContext2D.fillStyle", "fillStyle")}} property (this takes <a href="/en-US/docs/Learn/CSS/Building_blocks/Values_and_units#colors">color values</a> just like CSS properties do), then drawing a rectangle that covers the entire area of the canvas with the{{domxref("CanvasRenderingContext2D.fillRect", "fillRect")}} method (the first two parameters are the coordinates of the rectangle's top left hand corner; the last two are the width and height you want the rectangle drawn at — we told you those <code>width</code> and <code>height</code> variables would be useful)!</p>

<p>OK, our template is done and it's time to move on.</p>

<h2 id="2D_canvas_basics">2D canvas basics</h2>

<p>As we said above, all drawing operations are done by manipulating a {{domxref("CanvasRenderingContext2D")}} object (in our case, <code>ctx</code>). Many operations need to be given coordinates to pinpoint exactly where to draw something — the top left of the canvas is point (0, 0), the horizontal (x) axis runs from left to right, and the vertical (y) axis runs from top to bottom.</p>

<p><img alt="" class="internal" src="canvas_default_grid.png"></p>

<p>Drawing shapes tends to be done using the rectangle shape primitive, or by tracing a line along a certain path and then filling in the shape. Below we'll show how to do both.</p>

<h3 id="Simple_rectangles">Simple rectangles</h3>

<p>Let's start with some simple rectangles.</p>

<ol>
 <li>
  <p>First of all, take a copy of your newly coded canvas template (or make a local copy of <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/1_canvas_template.html">1_canvas_template.html</a> if you didn't follow the above steps).</p>
 </li>
 <li>
  <p>Next, add the following lines to the bottom of your JavaScript:</p>

  <pre class="brush: js">ctx.fillStyle = 'rgb(255, 0, 0)';
ctx.fillRect(50, 50, 100, 150);</pre>

  <p>If you save and refresh, you should see a red rectangle has appeared on your canvas. Its top left corner is 50 pixels away from the top and left of the canvas edge (as defined by the first two parameters), and it is 100 pixels wide and 150 pixels tall (as defined by the third and fourth parameters).</p>
 </li>
 <li>
  <p>Let's add another rectangle into the mix — a green one this time. Add the following at the bottom of your JavaScript:</p>

  <pre class="brush: js">ctx.fillStyle = 'rgb(0, 255, 0)';
ctx.fillRect(75, 75, 100, 100);</pre>

  <p>Save and refresh, and you'll see your new rectangle. This raises an important point: graphics operations like drawing rectangles, lines, and so forth are performed in the order in which they occur. Think of it like painting a wall, where each coat of paint overlaps and may even hide what's underneath. You can't do anything to change this, so you have to think carefully about the order in which you draw the graphics.</p>
 </li>
 <li>
  <p>Note that you can draw semi-transparent graphics by specifying a semi-transparent color, for example by using <code>rgba()</code>. The <code>a</code> value defines what's called the "alpha channel, " or the amount of transparency the color has. The higher its value, the more it will obscure whatever's behind it. Add the following to your code:</p>

  <pre class="brush: js">ctx.fillStyle = 'rgba(255, 0, 255, 0.75)';
ctx.fillRect(25, 100, 175, 50);</pre>
 </li>
 <li>
  <p>Now try drawing some more rectangles of your own; have fun!</p>
 </li>
</ol>

<h3 id="Strokes_and_line_widths">Strokes and line widths</h3>

<p>So far we've looked at drawing filled rectangles, but you can also draw rectangles that are just outlines (called <strong>strokes</strong> in graphic design). To set the color you want for your stroke, you use the {{domxref("CanvasRenderingContext2D.strokeStyle", "strokeStyle")}} property; drawing a stroke rectangle is done using {{domxref("CanvasRenderingContext2D.strokeRect", "strokeRect")}}.</p>

<ol>
 <li>
  <p>Add the following to the previous example, again below the previous JavaScript lines:</p>

  <pre class="brush: js">ctx.strokeStyle = 'rgb(255, 255, 255)';
ctx.strokeRect(25, 25, 175, 200);</pre>
 </li>
 <li>
  <p>The default width of strokes is 1 pixel; you can adjust the {{domxref("CanvasRenderingContext2D.lineWidth", "lineWidth")}} property value to change this (it takes a number representing the number of pixels wide the stroke is). Add the following line in between the previous two lines:</p>

  <pre class="brush: js">ctx.lineWidth = 5;</pre>
 </li>
</ol>

<p>Now you should see that your white outline has become much thicker! That's it for now. At this point your example should look like this:</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/apis/drawing-graphics/getting-started/2_canvas_rectangles.html", '100%', 250)}}</p>

<div class="note">
<p><strong>Note:</strong> The finished code is available on GitHub as <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/2_canvas_rectangles.html">2_canvas_rectangles.html</a>.</p>
</div>

<h3 id="Drawing_paths">Drawing paths</h3>

<p>If you want to draw anything more complex than a rectangle, you need to draw a path. Basically, this involves writing code to specify exactly what path the pen should move along on your canvas to trace the shape you want to draw. Canvas includes functions for drawing straight lines, circles, Bézier curves, and more.</p>

<p>Let's start the section off by making a fresh copy of our canvas template (<a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/1_canvas_template.html">1_canvas_template.html</a>), in which to draw the new example.</p>

<p>We'll be using some common methods and properties across all of the below sections:</p>

<ul>
 <li>{{domxref("CanvasRenderingContext2D.beginPath", "beginPath()")}} — start drawing a path at the point where the pen currently is on the canvas. On a new canvas, the pen starts out at (0, 0).</li>
 <li>{{domxref("CanvasRenderingContext2D.moveTo", "moveTo()")}} — move the pen to a different point on the canvas, without recording or tracing the line; the pen "jumps" to the new position.</li>
 <li>{{domxref("CanvasRenderingContext2D.fill", "fill()")}} — draw a filled shape by filling in the path you've traced so far.</li>
 <li>{{domxref("CanvasRenderingContext2D.stroke", "stroke()")}} — draw an outline shape by drawing a stroke along the path you've drawn so far.</li>
 <li>You can also use features like <code>lineWidth</code> and <code>fillStyle</code>/<code>strokeStyle</code> with paths as well as rectangles.</li>
</ul>

<p>A typical, simple path-drawing operation would look something like so:</p>

<pre class="brush: js">ctx.fillStyle = 'rgb(255, 0, 0)';
ctx.beginPath();
ctx.moveTo(50, 50);
// draw your path
ctx.fill();</pre>

<h4 id="Drawing_lines">Drawing lines</h4>

<p>Let's draw an equilateral triangle on the canvas.</p>

<ol>
 <li>
  <p>First of all, add the following helper function to the bottom of your code. This converts degree values to radians, which is useful because whenever you need to provide an angle value in JavaScript, it will nearly always be in radians, but humans usually think in degrees.</p>

  <pre class="brush: js">function degToRad(degrees) {
  return degrees * Math.PI / 180;
};</pre>
 </li>
 <li>
  <p>Next, start off your path by adding the following below your previous addition; here we set a color for our triangle, start drawing a path, and then move the pen to (50, 50) without drawing anything. That's where we'll start drawing our triangle.</p>

  <pre class="brush: js">ctx.fillStyle = 'rgb(255, 0, 0)';
ctx.beginPath();
ctx.moveTo(50, 50);</pre>
 </li>
 <li>
  <p>Now add the following lines at the bottom of your script:</p>

  <pre class="brush: js">ctx.lineTo(150, 50);
let triHeight = 50 * Math.tan(degToRad(60));
ctx.lineTo(100, 50+triHeight);
ctx.lineTo(50, 50);
ctx.fill();</pre>

  <p>Let's run through this in order:</p>

  <p>First we draw a line across to (150, 50) — our path now goes 100 pixels to the right along the x axis.</p>

  <p>Second, we work out the height of our equilateral triangle, using a bit of simple trigonometry. Basically, we are drawing the triangle pointing downwards. The angles in an equilateral triangle are always 60 degrees; to work out the height we can split it down the middle into two right-angled triangles, which will each have angles of 90 degrees, 60 degrees, and 30 degrees. In terms of the sides:</p>

  <ul>
   <li>The longest side is called the <strong>hypotenuse</strong></li>
   <li>The side next to the 60 degree angle is called the <strong>adjacent</strong> — which we know is 50 pixels, as it is half of the line we just drew.</li>
   <li>The side opposite the 60 degree angle is called the <strong>opposite</strong>, which is the height of the triangle we want to calculate.</li>
  </ul>

  <p><img alt="" src="trigonometry.png"></p>

  <p>One of the basic trigonometric formulae states that the length of the adjacent multiplied by the tangent of the angle is equal to the opposite, hence we come up with <code>50 * Math.tan(degToRad(60))</code>. We use our <code>degToRad()</code> function to convert 60 degrees to radians, as {{jsxref("Math.tan()")}} expects an input value in radians.</p>
 </li>
 <li>
  <p>With the height calculated, we draw another line to <code>(100, 50 + triHeight)</code>. The X coordinate is simple; it must be halfway between the previous two X values we set. The Y value on the other hand must be 50 plus the triangle height, as we know the top of the triangle is 50 pixels from the top of the canvas.</p>
 </li>
 <li>
  <p>The next line draws a line back to the starting point of the triangle.</p>
 </li>
 <li>
  <p>Last of all, we run <code>ctx.fill()</code> to end the path and fill in the shape.</p>
 </li>
</ol>

<h4 id="Drawing_circles">Drawing circles</h4>

<p>Now let's look at how to draw a circle in canvas. This is accomplished using the {{domxref("CanvasRenderingContext2D.arc", "arc()")}} method, which draws all or part of a circle at a specified point.</p>

<ol>
 <li>
  <p>Let's add an arc to our canvas — add the following to the bottom of your code:</p>

  <pre class="brush: js">ctx.fillStyle = 'rgb(0, 0, 255)';
ctx.beginPath();
ctx.arc(150, 106, 50, degToRad(0), degToRad(360), false);
ctx.fill();</pre>

  <p><code>arc()</code> takes six parameters. The first two specify the position of the arc's center (X and Y, respectively). The third is the circle's radius, the fourth and fifth are the start and end angles at which to draw the circle (so specifying 0 and 360 degrees gives us a full circle), and the sixth parameter defines whether the circle should be drawn counterclockwise (anticlockwise) or clockwise (<code>false</code> is clockwise).</p>

  <div class="note">
  <p><strong>Note:</strong> 0 degrees is horizontally to the right.</p>
  </div>
 </li>
 <li>
  <p>Let's try adding another arc:</p>

  <pre class="brush: js">ctx.fillStyle = 'yellow';
ctx.beginPath();
ctx.arc(200, 106, 50, degToRad(-45), degToRad(45), true);
ctx.lineTo(200, 106);
ctx.fill();</pre>

  <p>The pattern here is very similar, but with two differences:</p>

  <ul>
   <li>We have set the last parameter of <code>arc()</code> to <code>true</code>, meaning that the arc is drawn counterclockwise, which means that even though the arc is specified as starting at -45 degrees and ending at 45 degrees, we draw the arc around the 270 degrees not inside this portion. If you were to change <code>true</code> to <code>false</code> and then re-run the code, only the 90 degree slice of the circle would be drawn.</li>
   <li>Before calling <code>fill()</code>, we draw a line to the center of the circle. This means that we get the rather nice Pac-Man-style cutout rendered. If you removed this line (try it!) then re-ran the code, you'd get just an edge of the circle chopped off between the start and end point of the arc. This illustrates another important point of the canvas — if you try to fill an incomplete path (i.e. one that is not closed), the browser fills in a straight line between the start and end point and then fills it in.</li>
  </ul>
 </li>
</ol>

<p>That's it for now; your final example should look like this:</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/apis/drawing-graphics/getting-started/3_canvas_paths.html", '100%', 200)}}</p>

<div class="note">
<p><strong>Note:</strong> The finished code is available on GitHub as <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/3_canvas_paths.html">3_canvas_paths.html</a>.</p>
</div>

<div class="note">
<p><strong>Note:</strong> To find out more about advanced path drawing features such as Bézier curves, check out our <a href="/en-US/docs/Web/API/Canvas_API/Tutorial/Drawing_shapes">Drawing shapes with canvas</a> tutorial.</p>
</div>

<h3 id="Text">Text</h3>

<p>Canvas also has features for drawing text. Let's explore these briefly. Start by making another fresh copy of our canvas template (<a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/1_canvas_template.html">1_canvas_template.html</a>) in which to draw the new example.</p>

<p>Text is drawn using two methods:</p>

<ul>
 <li>{{domxref("CanvasRenderingContext2D.fillText", "fillText()")}} — draws filled text.</li>
 <li>{{domxref("CanvasRenderingContext2D.strokeText", "strokeText()")}} — draws outline (stroke) text.</li>
</ul>

<p>Both of these take three properties in their basic usage: the text string to draw and the X and Y coordinates of the point to start drawing the text at. This works out as the <strong>bottom left</strong> corner of the <strong>text box</strong> (literally, the box surrounding the text you draw), which might confuse you as other drawing operations tend to start from the top left corner — bear this in mind.</p>

<p>There are also a number of properties to help control text rendering such as {{domxref("CanvasRenderingContext2D.font", "font")}}, which lets you specify font family, size, etc. It takes as its value the same syntax as the CSS {{cssxref("font")}} property.</p>

<p>Try adding the following block to the bottom of your JavaScript:</p>

<pre class="brush: js">ctx.strokeStyle = 'white';
ctx.lineWidth = 1;
ctx.font = '36px arial';
ctx.strokeText('Canvas text', 50, 50);

ctx.fillStyle = 'red';
ctx.font = '48px georgia';
ctx.fillText('Canvas text', 50, 150);</pre>

<p>Here we draw two lines of text, one outline and the other stroke. The final example should look like so:</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/apis/drawing-graphics/getting-started/4_canvas_text.html", '100%', 180)}}</p>

<div class="note">
<p><strong>Note:</strong> The finished code is available on GitHub as <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/4_canvas_text.html">4_canvas_text.html</a>.</p>
</div>

<p>Have a play and see what you can come up with! You can find more information on the options available for canvas text at <a href="/en-US/docs/Web/API/Canvas_API/Tutorial/Drawing_text">Drawing text</a>.</p>

<h3 id="Drawing_images_onto_canvas">Drawing images onto canvas</h3>

<p>It is possible to render external images onto your canvas. These can be simple images, frames from videos, or the content of other canvases. For the moment we'll just look at the case of using some simple images on our canvas.</p>

<ol>
 <li>
  <p>As before, make another fresh copy of our canvas template (<a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/1_canvas_template.html">1_canvas_template.html</a>) in which to draw the new example. In this case you'll also need to save a copy of our sample image — <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/firefox.png">firefox.png</a> — in the same directory.</p>

  <p>Images are drawn onto canvas using the {{domxref("CanvasRenderingContext2D.drawImage", "drawImage()")}} method. The simplest version takes three parameters — a reference to the image you want to render, and the X and Y coordinates of the image's top left corner.</p>
 </li>
 <li>
  <p>Let's start by getting an image source to embed in our canvas. Add the following lines to the bottom of your JavaScript:</p>

  <pre class="brush: js">let image = new Image();
image.src = 'firefox.png';</pre>

  <p>Here we create a new {{domxref("HTMLImageElement")}} object using the {{domxref("HTMLImageElement.Image()", "Image()")}} constructor. The returned object is the same type as that which is returned when you grab a reference to an existing {{htmlelement("img")}} element). We then set its {{htmlattrxref("src", "img")}} attribute to equal our Firefox logo image. At this point, the browser starts loading the image.</p>
 </li>
 <li>
  <p>We could now try to embed the image using <code>drawImage()</code>, but we need to make sure the image file has been loaded first, otherwise the code will fail. We can achieve this using the <code>onload</code> event handler, which will only be invoked when the image has finished loading. Add the following block below the previous one:</p>

  <pre class="brush: js">image.onload = function() {
  ctx.drawImage(image, 50, 50);
}</pre>

  <p>If you load your example in the browser now, you should see the image embedded in the canvas.</p>
 </li>
 <li>
  <p>But there's more! What if we want to display only a part of the image, or to resize it? We can do both with the more complex version of <code>drawImage()</code>. Update your <code>ctx.drawImage()</code> line like so:</p>

  <pre class="brush: js">ctx.drawImage(image, 20, 20, 185, 175, 50, 50, 185, 175);</pre>

  <ul>
   <li>The first parameter is the image reference, as before.</li>
   <li>Parameters 2 and 3 define the coordinates of the top left corner of the area you want to cut out of the loaded image, relative to the top-left corner of the image itself. Nothing to the left of the first parameter or above the second will be drawn.</li>
   <li>Parameters 4 and 5 define the width and height of the area we want to cut out from the original image we loaded.</li>
   <li>Parameters 6 and 7 define the coordinates at which you want to draw the top-left corner of the cut-out portion of the image, relative to the top-left corner of the canvas.</li>
   <li>Parameters 8 and 9 define the width and height to draw the cut-out area of the image. In this case, we have specified the same dimensions as the original slice, but you could resize it by specifying different values.</li>
  </ul>
 </li>
</ol>

<p>The final example should look like so:</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/apis/drawing-graphics/getting-started/5_canvas_images.html", '100%', 260)}}</p>

<div class="note">
<p><strong>Note:</strong> The finished code is available on GitHub as <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/5_canvas_images.html">5_canvas_images.html</a>.</p>
</div>

<h2 id="Loops_and_animations">Loops and animations</h2>

<p>We have so far covered some very basic uses of 2D canvas, but really you won't experience the full power of canvas unless you update or animate it in some way. After all, canvas does provide scriptable images! If you aren't going to change anything, then you might as well just use static images and save yourself all the work.</p>

<h3 id="Creating_a_loop">Creating a loop</h3>

<p>Playing with loops in canvas is rather fun — you can run canvas commands inside a <code><a href="/en-US/docs/Web/JavaScript/Reference/Statements/for">for</a></code> (or other type of) loop just like any other JavaScript code.</p>

<p>Let's build a simple example.</p>

<ol>
 <li>
  <p>Make another fresh copy of our canvas template (<a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/1_canvas_template.html">1_canvas_template.html</a>) and open it in your code editor.</p>
 </li>
 <li>
  <p>Add the following line to the bottom of your JavaScript. This contains a new method, {{domxref("CanvasRenderingContext2D.translate", "translate()")}}, which moves the origin point of the canvas:</p>

  <pre class="brush: js">ctx.translate(width/2, height/2);</pre>

  <p>This causes the coordinate origin (0, 0) to be moved to the center of the canvas, rather than being at the top left corner. This is very useful in many situations, like this one, where we want our design to be drawn relative to the center of the canvas.</p>
 </li>
 <li>
  <p>Now add the following code to the bottom of the JavaScript:</p>

  <pre class="brush: js">function degToRad(degrees) {
  return degrees * Math.PI / 180;
};

function rand(min, max) {
  return Math.floor(Math.random() * (max-min+1)) + (min);
}

let length = 250;
let moveOffset = 20;

for(var i = 0; i &lt; length; i++) {

}</pre>

  <p>Here we are implementing the same <code>degToRad()</code> function we saw in the triangle example above, a <code>rand()</code> function that returns a random number between given lower and upper bounds, <code>length</code> and <code>moveOffset</code> variables (which we'll find out more about later), and an empty <code>for</code> loop.</p>
 </li>
 <li>
  <p>The idea here is that we'll draw something on the canvas inside the <code>for</code> loop, and iterate on it each time so we can create something interesting. Add the following code inside your <code>for</code> loop:</p>

  <pre class="brush: js">ctx.fillStyle = 'rgba(' + (255-length) + ', 0, ' + (255-length) + ', 0.9)';
ctx.beginPath();
ctx.moveTo(moveOffset, moveOffset);
ctx.lineTo(moveOffset+length, moveOffset);
let triHeight = length/2 * Math.tan(degToRad(60));
ctx.lineTo(moveOffset+(length/2), moveOffset+triHeight);
ctx.lineTo(moveOffset, moveOffset);
ctx.fill();

length--;
moveOffset += 0.7;
ctx.rotate(degToRad(5));</pre>

  <p>So on each iteration, we:</p>

  <ul>
   <li>Set the <code>fillStyle</code> to be a shade of slightly transparent purple, which changes each time based on the value of <code>length</code>. As you'll see later the length gets smaller each time the loop runs, so the effect here is that the color gets brighter with each successive triangle drawn.</li>
   <li>Begin the path.</li>
   <li>Move the pen to a coordinate of <code>(moveOffset, moveOffset)</code>; This variable defines how far we want to move each time we draw a new triangle.</li>
   <li>Draw a line to a coordinate of <code>(moveOffset+length, moveOffset)</code>. This draws a line of length <code>length</code> parallel to the X axis.</li>
   <li>Calculate the triangle's height, as before.</li>
   <li>Draw a line to the downward-pointing corner of the triangle, then draw a line back to the start of the triangle.</li>
   <li>Call <code>fill()</code> to fill in the triangle.</li>
   <li>Update the variables that describe the sequence of triangles, so we can be ready to draw the next one. We decrease the <code>length</code> value by 1, so the triangles get smaller each time; increase <code>moveOffset</code> by a small amount so each successive triangle is slightly further away, and use another new function, {{domxref("CanvasRenderingContext2D.rotate", "rotate()")}}, which allows us to rotate the entire canvas! We rotate it by 5 degrees before drawing the next triangle.</li>
  </ul>
 </li>
</ol>

<p>That's it! The final example should look like so:</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/apis/drawing-graphics/loops_animation/6_canvas_for_loop.html", '100%', 550)}}</p>

<p>At this point, we'd like to encourage you to play with the example and make it your own! For example:</p>

<ul>
 <li>Draw rectangles or arcs instead of triangles, or even embed images.</li>
 <li>Play with the <code>length</code> and <code>moveOffset</code> values.</li>
 <li>Introduce some random numbers using that <code>rand()</code> function we included above but didn't use.</li>
</ul>

<div class="note">
<p><strong>Note:</strong> The finished code is available on GitHub as <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/loops_animation/6_canvas_for_loop.html">6_canvas_for_loop.html</a>.</p>
</div>

<h3 id="Animations">Animations</h3>

<p>The loop example we built above was fun, but really you need a constant loop that keeps going and going for any serious canvas applications (such as games and real time visualizations). If you think of your canvas as being like a movie, you really want the display to update on each frame to show the updated view, with an ideal refresh rate of 60 frames per second so that movement appears nice and smooth to the human eye.</p>

<p>There are a few JavaScript functions that will allow you to run functions repeatedly, several times a second, the best one for our purposes here being {{domxref("window.requestAnimationFrame()")}}. It takes one parameter — the name of the function you want to run for each frame. The next time the browser is ready to update the screen, your function will get called. If that function draws the new update to your animation, then calls <code>requestAnimationFrame()</code> again just before the end of the function, the animation loop will continue to run. The loop ends when you stop calling <code>requestAnimationFrame()</code> or if you call {{domxref("window.cancelAnimationFrame()")}} after calling <code>requestAnimationFrame()</code> but before the frame is called.</p>

<div class="note">
<p><strong>Note:</strong> It's good practice to call <code>cancelAnimationFrame()</code> from your main code when you're done using the animation, to ensure that no updates are still waiting to be run.</p>
</div>

<p>The browser works out complex details such as making the animation run at a consistent speed, and not wasting resources animating things that can't be seen.</p>

<p>To see how it works, let's quickly look again at our Bouncing Balls example (<a href="https://mdn.github.io/learning-area/javascript/oojs/bouncing-balls/index-finished.html">see it live</a>, and also see <a href="https://github.com/mdn/learning-area/tree/master/javascript/oojs/bouncing-balls">the source code</a>). The code for the loop that keeps everything moving looks like this:</p>

<pre class="brush: js">function loop() {
  ctx.fillStyle = 'rgba(0, 0, 0, 0.25)';
  ctx.fillRect(0, 0, width, height);

  for(let i = 0; i &lt; balls.length; i++) {
    balls[i].draw();
    balls[i].update();
    balls[i].collisionDetect();
  }

  requestAnimationFrame(loop);
}

loop();</pre>

<p>We run the <code>loop()</code> function once at the bottom of the code to start the cycle, drawing the first animation frame; the <code>loop()</code> function then takes charge of calling <code>requestAnimationFrame(loop)</code> to run the next frame of the animation, again and again.</p>

<p>Note that on each frame we are completely clearing the canvas and redrawing everything. For every ball present we draw it, update its position, and check to see if it is colliding with any other balls. Once you've drawn a graphic to a canvas, there's no way to manipulate that graphic individually like you can with DOM elements. You can't move each ball around on the canvas, because once it's drawn, it's part of the canvas, and is not an individual accessible element or object. Instead, you have to erase and redraw, either by erasing the entire frame and redrawing everything, or by having code that knows exactly what parts need to be erased and only erases and redraws the minimum area of the canvas necessary.</p>

<p>Optimizing animation of graphics is an entire specialty of programming, with lots of clever techniques available. Those are beyond what we need for our example, though!</p>

<p>In general, the process of doing a canvas animation involves the following steps:</p>

<ol>
 <li>Clear the canvas contents (e.g. with {{domxref("CanvasRenderingContext2D.fillRect", "fillRect()")}} or {{domxref("CanvasRenderingContext2D.clearRect", "clearRect()")}}).</li>
 <li>Save state (if necessary) using {{domxref("CanvasRenderingContext2D.save", "save()")}} — this is needed when you want to save settings you've updated on the canvas before continuing, which is useful for more advanced applications.</li>
 <li>Draw the graphics you are animating.</li>
 <li>Restore the settings you saved in step 2, using {{domxref("CanvasRenderingContext2D.restore", "restore()")}}</li>
 <li>Call <code>requestAnimationFrame()</code> to schedule drawing of the next frame of the animation.</li>
</ol>

<div class="note">
<p><strong>Note:</strong> We won't cover <code>save()</code> and <code>restore()</code> here, but they are explained nicely in our <a href="/en-US/docs/Web/API/Canvas_API/Tutorial/Transformations">Transformations</a> tutorial (and the ones that follow it).</p>
</div>

<h3 id="A_simple_character_animation">A simple character animation</h3>

<p>Now let's create our own simple animation — we'll get a character from a certain rather awesome retro computer game to walk across the screen.</p>

<ol>
 <li>
  <p>Make another fresh copy of our canvas template (<a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/getting-started/1_canvas_template.html">1_canvas_template.html</a>) and open it in your code editor. Make a copy of <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/loops_animation/walk-right.png">walk-right.png</a> in the same directory.</p>
 </li>
 <li>
  <p>At the bottom of the JavaScript, add the following line to once again make the coordinate origin sit in the middle of the canvas:</p>

  <pre class="brush: js">ctx.translate(width/2, height/2);</pre>
 </li>
 <li>
  <p>Now let's create a new {{domxref("HTMLImageElement")}} object, set its {{htmlattrxref("src", "img")}} to the image we want to load, and add an <code>onload</code> event handler that will cause the <code>draw()</code> function to fire when the image is loaded:</p>

  <pre class="brush: js">let image = new Image();
image.src = 'walk-right.png';
image.onload = draw;</pre>
 </li>
 <li>
  <p>Now we'll add some variables to keep track of the position the sprite is to be drawn on the screen, and the sprite number we want to display.</p>

  <pre class="brush: js">let sprite = 0;
let posX = 0;</pre>

  <p>Let's explain the spritesheet image (which we have respectfully borrowed from Mike Thomas' <a href="https://atomicrobotdesign.com/blog/htmlcss/create-a-sprite-sheet-walk-cycle-using-using-css-animation/" rel="bookmark" title="Permanent Link to Create a sprite sheet walk cycle using CSS animation">Create a sprite sheet walk cycle using CSS animation</a>). The image looks like this:</p>

  <p><img alt="" src="walk-right.png"></p>

  <p>It contains six sprites that make up the whole walking sequence — each one is 102 pixels wide and 148 pixels high. To display each sprite cleanly we will have to use <code>drawImage()</code> to chop out a single sprite image from the spritesheet and display only that part, like we did above with the Firefox logo. The X coordinate of the slice will have to be a multiple of 102, and the Y coordinate will always be 0. The slice size will always be 102 by 148 pixels.</p>
 </li>
 <li>
  <p>Now let's insert an empty <code>draw()</code> function at the bottom of the code, ready for filling up with some code:</p>

  <pre class="brush: js">function draw() {

};</pre>
 </li>
 <li>
  <p>the rest of the code in this section goes inside <code>draw()</code>. First, add the following line, which clears the canvas to prepare for drawing each frame. Notice that we have to specify the top-left corner of the rectangle as <code>-(width/2), -(height/2)</code> because we specified the origin position as <code>width/2, height/2</code> earlier on.</p>

  <pre class="brush: js">ctx.fillRect(-(width/2), -(height/2), width, height);</pre>
 </li>
 <li>
  <p>Next, we'll draw our image using drawImage — the 9-parameter version. Add the following:</p>

  <pre class="brush: js">ctx.drawImage(image, (sprite*102), 0, 102, 148, 0+posX, -74, 102, 148);</pre>

  <p>As you can see:</p>

  <ul>
   <li>We specify <code>image</code> as the image to embed.</li>
   <li>Parameters 2 and 3 specify the top-left corner of the slice to cut out of the source image, with the X value as <code>sprite</code> multiplied by 102 (where <code>sprite</code> is the sprite number between 0 and 5) and the Y value always 0.</li>
   <li>Parameters 4 and 5 specify the size of the slice to cut out — 102 pixels by 148 pixels.</li>
   <li>Parameters 6 and 7 specify the top-left corner of the box into which to draw the slice on the canvas — the X position is 0 + <code>posX</code>, meaning that we can alter the drawing position by altering the <code>posX</code> value.</li>
   <li>Parameters 8 and 9 specify the size of the image on the canvas. We just want to keep its original size, so we specify 102 and 148 as the width and height.</li>
  </ul>
 </li>
 <li>
  <p>Now we'll alter the <code>sprite</code> value after each draw — well, after some of them anyway. Add the following block to the bottom of the <code>draw()</code> function:</p>

  <pre class="brush: js">  if (posX % 13 === 0) {
    if (sprite === 5) {
      sprite = 0;
    } else {
      sprite++;
    }
  }</pre>

  <p>We are wrapping the whole block in <code> if (posX % 13 === 0) { ... }</code>. We use the modulo (<code>%</code>) operator (also known as the <a href="/en-US/docs/Web/JavaScript/Reference/Operators#remainder_()">remainder operator</a>) to check whether the <code>posX</code> value can be exactly divided by 13 with no remainder. If so, we move on  to the next sprite by incrementing <code>sprite</code> (wrapping to 0 after we're done with sprite #5). This effectively means that we are only updating the sprite on every 13th frame, or roughly about 5 frames a second (<code>requestAnimationFrame()</code> calls us at up to 60 frames per second if possible). We are deliberately slowing down the frame rate because we only have six sprites to work with, and if we display one every 60th of a second, our character will move way too fast!</p>

  <p>Inside the outer block we use an <code><a href="/en-US/docs/Web/JavaScript/Reference/Statements/if...else">if ... else</a></code> statement to check whether the <code>sprite</code> value is at 5 (the last sprite, given that the sprite numbers run from 0 to 5). If we are showing the last sprite already, we reset <code>sprite</code> back to 0; if not we just increment it by 1.</p>
 </li>
 <li>
  <p>Next we need to work out how to change the <code>posX</code> value on each frame — add the following code block just below your last one. </p>

  <pre class="brush: js">  if(posX &gt; width/2) {
    newStartPos = -((width/2) + 102);
    posX = Math.ceil(newStartPos);
    console.log(posX);
  } else {
    posX += 2;
  }</pre>

  <p>We are using another <code>if ... else</code> statement to see if the value of <code>posX</code> has become greater than <code>width/2</code>, which means our character has walked off the right edge of the screen. If so, we calculate a position that would put the character just to the left of the left side of the screen.</p>

  <p>If our character hasn't yet walked off the edge of the screen, we increment <code>posX</code> by 2. This will make him move a little bit to the right the next time we draw him.</p>
 </li>
 <li>
  <p>Finally, we need to make the animation loop by calling {{domxref("window.requestAnimationFrame", "requestAnimationFrame()")}} at the bottom of the <code>draw()</code> function:</p>

  <pre class="brush: js">window.requestAnimationFrame(draw);</pre>
 </li>
</ol>

<p>That's it! The final example should look like so:</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/apis/drawing-graphics/loops_animation/7_canvas_walking_animation.html", '100%', 260)}}</p>

<div class="note">
<p><strong>Note:</strong> The finished code is available on GitHub as <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/loops_animation/7_canvas_walking_animation.html">7_canvas_walking_animation.html</a>.</p>
</div>

<h3 id="A_simple_drawing_application">A simple drawing application</h3>

<p>As a final animation example, we'd like to show you a very simple drawing application, to illustrate how the animation loop can be combined with user input (like mouse movement, in this case). We won't get you to walk through and build this one; we'll just explore the most interesting parts of the code.</p>

<p>The example can be found on GitHub as <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/loops_animation/8_canvas_drawing_app.html">8_canvas_drawing_app.html</a>, and you can play with it live below:</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/apis/drawing-graphics/loops_animation/8_canvas_drawing_app.html", '100%', 600)}}</p>

<p>Let's look at the most interesting parts. First of all, we keep track of the mouse's X and Y coordinates and whether it is being clicked or not with three variables: <code>curX</code>, <code>curY</code>, and <code>pressed</code>. When the mouse moves, we fire a function set as the <code>onmousemove</code> event handler, which captures the current X and Y values. We also use <code>onmousedown</code> and <code>onmouseup</code> event handlers to change the value of <code>pressed</code> to <code>true</code> when the mouse button is pressed, and back to <code>false</code> again when it is released.</p>

<pre class="brush: js">let curX;
let curY;
let pressed = false;

document.onmousemove = function(e) {
  curX = (window.Event) ? e.pageX : e.clientX + (document.documentElement.scrollLeft ? document.documentElement.scrollLeft : document.body.scrollLeft);
  curY = (window.Event) ? e.pageY : e.clientY + (document.documentElement.scrollTop ? document.documentElement.scrollTop : document.body.scrollTop);
}

canvas.onmousedown = function() {
  pressed = true;
};

canvas.onmouseup = function() {
  pressed = false;
}</pre>

<p>When the "Clear canvas" button is pressed, we run a simple function that clears the whole canvas back to black, the same way we've seen before:</p>

<pre class="brush: js">clearBtn.onclick = function() {
  ctx.fillStyle = 'rgb(0, 0, 0)';
  ctx.fillRect(0, 0, width, height);
}</pre>

<p>The drawing loop is pretty simple this time around — if pressed is <code>true</code>, we draw a circle with a fill style equal to the value in the color picker, and a radius equal to the value set in the range input. We have to draw the circle 85 pixels above where we measured it from, because the vertical measurement is taken from the top of the viewport, but we are drawing the circle relative to the top of the canvas, which starts below the 85 pixel-high toolbar. If we drew it with just <code>curY</code> as the y coordinate, it would appear 85 pixels lower than the mouse position.</p>

<pre class="brush: js">function draw() {
  if(pressed) {
    ctx.fillStyle = colorPicker.value;
    ctx.beginPath();
    ctx.arc(curX, curY-85, sizePicker.value, degToRad(0), degToRad(360), false);
    ctx.fill();
  }

  requestAnimationFrame(draw);
}

draw();</pre>

<div class="note">
<p><strong>Note:</strong> The {{htmlelement("input")}} <code>range</code> and <code>color</code> types are supported fairly well across browsers, with the exception of Internet Explorer versions less than 10; also Safari doesn't yet support <code>color</code>. If your browser doesn't support these inputs, they will fall back to simple text fields and you'll just have to enter valid color/number values yourself.</p>
</div>

<h2 id="WebGL">WebGL</h2>

<p>It's now time to leave 2D behind, and take a quick look at 3D canvas. 3D canvas content is specified using the <a href="/en-US/docs/Web/API/WebGL_API">WebGL API</a>, which is a completely separate API from the 2D canvas API, even though they both render onto {{htmlelement("canvas")}} elements.</p>

<p>WebGL is based on <a href="/en-US/docs/Glossary/OpenGL">OpenGL</a> (Open Graphics Library), and allows you to communicate directly with the computer's <a href="/en-US/docs/Glossary/GPU">GPU</a>. As such, writing raw WebGL is closer to low level languages such as C++ than regular JavaScript; it is quite complex but incredibly powerful.</p>

<h3 id="Using_a_library">Using a library</h3>

<p>Because of its complexity, most people write 3D graphics code using a third party JavaScript library such as <a href="/en-US/docs/Games/Techniques/3D_on_the_web/Building_up_a_basic_demo_with_Three.js">Three.js</a>, <a href="/en-US/docs/Games/Techniques/3D_on_the_web/Building_up_a_basic_demo_with_PlayCanvas">PlayCanvas</a>, or <a href="/en-US/docs/Games/Techniques/3D_on_the_web/Building_up_a_basic_demo_with_Babylon.js">Babylon.js</a>. Most of these work in a similar way, providing functionality to create primitive and custom shapes, position viewing cameras and lighting, covering surfaces with textures, and more. They handle the WebGL for you, letting you work on a higher level.</p>

<p>Yes, using one of these means learning another new API (a third party one, in this case), but they are a lot simpler than coding raw WebGL.</p>

<h3 id="Recreating_our_cube">Recreating our cube</h3>

<p>Let's look at a simple example of how to create something with a WebGL library. We'll choose <a href="/en-US/docs/Games/Techniques/3D_on_the_web/Building_up_a_basic_demo_with_Three.js">Three.js</a>, as it is one of the most popular ones. In this tutorial we'll create the 3D spinning cube we saw earlier.</p>

<ol>
 <li>
  <p>To start with, make a local copy of <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/threejs-cube/index.html">index.html</a> in a new folder, then save a copy of <a href="https://github.com/mdn/learning-area/blob/master/javascript/apis/drawing-graphics/threejs-cube/metal003.png">metal003.png</a> in the same folder. This is the image we'll use as a surface texture for the cube later on.</p>
 </li>
 <li>
  <p>Next, create a new file called <code>main.js</code>, again in the same folder as before.</p>
 </li>
 <li>
  <p>If you open <code>index.html</code> in your code editor, you'll see that it has two {{htmlelement("script")}} elements — the first one attaching <code>three.min.js</code> to the page, and the second one attaching our <code>main.js</code> file to the page. You need to <a href="https://raw.githubusercontent.com/mrdoob/three.js/dev/build/three.min.js">download the three.min.js library</a> and save it in the same directory as before.</p>
 </li>
 <li>
  <p>Now we've got <code>three.js</code> attached to our page, we can start to write JavaScript that makes use of it into <code>main.js</code>. Let's start by creating a new scene — add the following into your main.js file:</p>

  <pre class="brush: js">const scene = new THREE.Scene();</pre>

  <p>The <code><a href="https://threejs.org/docs/index.html#api/en/scenes/Scene">Scene()</a></code> constructor creates a new scene, which represents the whole 3D world we are trying to display.</p>
 </li>
 <li>
  <p>Next, we need a <strong>camera</strong> so we can see the scene. In 3D imagery terms, the camera represents a viewer's position in the world. To create a camera, add the following lines next:</p>

  <pre class="brush: js">const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
camera.position.z = 5;
</pre>

  <p>The <code><a href="https://threejs.org/docs/index.html#api/en/cameras/PerspectiveCamera">PerspectiveCamera()</a></code> constructor takes four arguments:</p>

  <ul>
   <li>The field of view: How wide the area in front of the camera is that should be visible onscreen, in degrees.</li>
   <li>The aspect ratio: Usually, this is the ratio of the scene's width divided by the scene's height. Using another value will distort the scene (which might be what you want, but usually isn't).</li>
   <li>The near plane: How close to the camera objects can be before we stop rendering them to the screen. Think about how when you move your fingertip closer and closer to the space between your eyes, eventually you can't see it anymore.</li>
   <li>The far plane: How far away things are from the camera before they are no longer rendered.</li>
  </ul>

  <p>We also set the camera's position to be 5 distance units out of the Z axis, which, like in CSS, is out of the screen towards you, the viewer.</p>
 </li>
 <li>
  <p>The third vital ingredient is a renderer. This is an object that renders a given scene, as viewed through a given camera. We'll create one for now using the <code><a href="https://threejs.org/docs/index.html#api/en/renderers/WebGLRenderer">WebGLRenderer()</a></code> constructor, but we'll not use it till later. Add the following lines next:</p>

  <pre class="brush: js">const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);</pre>

  <p>The first line creates a new renderer, the second line sets the size at which the renderer will draw the camera's view, and the third line appends the {{htmlelement("canvas")}} element created by the renderer to the document's {{htmlelement("body")}}. Now anything the renderer draws will be displayed in our window.</p>
 </li>
 <li>
  <p>Next, we want to create the cube we'll display on the canvas. Add the following chunk of code at the bottom of your JavaScript:</p>

  <pre class="brush: js">let cube;

let loader = new THREE.TextureLoader();

loader.load( 'metal003.png', function (texture) {
  texture.wrapS = THREE.RepeatWrapping;
  texture.wrapT = THREE.RepeatWrapping;
  texture.repeat.set(2, 2);

  let geometry = new THREE.BoxGeometry(2.4, 2.4, 2.4);
  let material = new THREE.MeshLambertMaterial( { map: texture, shading: THREE.FlatShading } );
  cube = new THREE.Mesh(geometry, material);
  scene.add(cube);

  draw();
});</pre>

  <p>There's a bit more to take in here, so let's go through it in stages:</p>

  <ul>
   <li>We first create a <code>cube</code> global variable so we can access our cube from anywhere in the code.</li>
   <li>Next, we create a new <code><a href="https://threejs.org/docs/index.html#api/en/loaders/TextureLoader">TextureLoader</a></code> object, then call <code>load()</code> on it. <code>load()</code> takes two parameters in this case (although it can take more): the texture we want to load (our PNG), and a function that will run when the texture has loaded.</li>
   <li>Inside this function we use properties of the <code><a href="https://threejs.org/docs/index.html#api/en/textures/Texture">texture</a></code> object to specify that we want a 2 x 2 repeat of the image wrapped around all sides of the cube. Next, we create a new <code><a href="https://threejs.org/docs/index.html#api/en/geometries/BoxGeometry">BoxGeometry</a></code> object and a new <code><a href="https://threejs.org/docs/index.html#api/en/materials/MeshLambertMaterial">MeshLambertMaterial</a></code> object, and bring them together in a <code><a href="https://threejs.org/docs/index.html#api/en/objects/Mesh">Mesh</a></code> to create our cube. An object typically requires a geometry (what shape it is) and a material (what its surface looks like).</li>
   <li>Last of all, we add our cube to the scene, then call our <code>draw()</code> function to start off the animation.</li>
  </ul>
 </li>
 <li>
  <p>Before we get to defining <code>draw()</code>, we'll add a couple of lights to the scene, to liven things up a bit; add the following blocks next:</p>

  <pre class="brush: js">let light = new THREE.AmbientLight('rgb(255, 255, 255)'); // soft white light
scene.add(light);

let spotLight = new THREE.SpotLight('rgb(255, 255, 255)');
spotLight.position.set( 100, 1000, 1000 );
spotLight.castShadow = true;
scene.add(spotLight);</pre>

  <p>An <code><a href="https://threejs.org/docs/index.html#api/en/lights/AmbientLight">AmbientLight</a></code> object is a kind of soft light that lightens the whole scene a bit, like the sun when you are outside. The <code><a href="https://threejs.org/docs/index.html#api/en/lights/SpotLight">SpotLight</a></code> object, on the other hand, is a directional beam of light, more like a flashlight/torch (or a spotlight, in fact).</p>
 </li>
 <li>
  <p>Last of all, let's add our <code>draw()</code> function to the bottom of the code:</p>

  <pre class="brush: js">function draw() {
  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;
  renderer.render(scene, camera);

  requestAnimationFrame(draw);
}</pre>

  <p>This is fairly intuitive; on each frame, we rotate our cube slightly on its X and Y axes, then render the scene as viewed by our camera, then finally call <code>requestAnimationFrame()</code> to schedule drawing our next frame.</p>
 </li>
</ol>

<p>Let's have another quick look at what the finished product should look like:</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/apis/drawing-graphics/threejs-cube/index.html", '100%', 500)}}</p>

<p>You can <a href="https://github.com/mdn/learning-area/tree/master/javascript/apis/drawing-graphics/threejs-cube">find the finished code on GitHub</a>.</p>

<div class="note">
<p><strong>Note:</strong> In our GitHub repo you can also find another interesting 3D cube example — <a href="https://github.com/mdn/learning-area/tree/master/javascript/apis/drawing-graphics/threejs-video-cube">Three.js Video Cube</a> (<a href="https://mdn.github.io/learning-area/javascript/apis/drawing-graphics/threejs-video-cube/">see it live also</a>). This uses {{domxref("MediaDevices.getUserMedia", "getUserMedia()")}} to take a video stream from a computer web cam and project it onto the side of the cube as a texture!</p>
</div>

<h2 id="Summary">Summary</h2>

<p>At this point, you should have a useful idea of the basics of graphics programming using Canvas and WebGL and what you can do with these APIs, as well as a good idea of where to go for further information. Have fun!</p>

<h2 id="See_also">See also</h2>

<p>Here we have covered only the real basics of canvas — there is so much more to learn! The below articles will take you further.</p>

<ul>
 <li><a href="/en-US/docs/Web/API/Canvas_API/Tutorial">Canvas tutorial</a> — A very detailed tutorial series explaining what you should know about 2D canvas in much more detail than was covered here. Essential reading.</li>
 <li><a href="/en-US/docs/Web/API/WebGL_API/Tutorial">WebGL tutorial</a> — A series that teaches the basics of raw WebGL programming.</li>
 <li><a href="/en-US/docs/Games/Techniques/3D_on_the_web/Building_up_a_basic_demo_with_Three.js">Building up a basic demo with Three.js</a> — basic Three.js tutorial. We also have equivalent guides for <a href="/en-US/docs/Games/Techniques/3D_on_the_web/Building_up_a_basic_demo_with_PlayCanvas">PlayCanvas</a> or <a href="/en-US/docs/Games/Techniques/3D_on_the_web/Building_up_a_basic_demo_with_Babylon.js">Babylon.js</a>.</li>
 <li><a href="/en-US/docs/Games">Game development</a> — the landing page for web games development on MDN. There are some really useful tutorials and techniques available here related to 2D and 3D canvas — see the Techniques and Tutorials menu options.</li>
</ul>

<h2 id="Examples">Examples</h2>

<ul>
 <li><a href="https://github.com/mdn/violent-theremin">Violent theramin</a> — Uses the Web Audio API to generate sound, and canvas to generate a pretty visualization to go along with it.</li>
 <li><a href="https://github.com/mdn/voice-change-o-matic">Voice change-o-matic</a> — Uses a canvas to visualize real-time audio data from the Web Audio API.</li>
</ul>

<p>{{PreviousMenuNext("Learn/JavaScript/Client-side_web_APIs/Third_party_APIs", "Learn/JavaScript/Client-side_web_APIs/Video_and_audio_APIs", "Learn/JavaScript/Client-side_web_APIs")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction">Introduction to web APIs</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Manipulating_documents">Manipulating documents</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Fetching_data">Fetching data from the server</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Third_party_APIs">Third party APIs</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Drawing_graphics">Drawing graphics</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Video_and_audio_APIs">Video and audio APIs</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Client-side_storage">Client-side storage</a></li>
</ul>