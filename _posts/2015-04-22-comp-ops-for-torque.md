---
layout:     post
title:      Composite Operations in Torque
date:       2015-04-22 13:00:00
summary:    Experiments and findings from around the internets
categories: torque comp-ops
published: true
---

# Comp-opping

There are [33 compositing operations](https://www.mapbox.com/tilemill/docs/guides/comp-op/) listed in the documentation for CartoCSS/Mapnik. The different blending modes are listed below with links to an appropriate description (such as Wikipedia). There's another undocumented one called `clear` that's not very interesting: it just clears all of the data from the screen.

<table>
  <col />
  <col />
  <col style="background: rgba(255,165,0,0.5);"/>
  <tr>
    <th>Color modes</th>
    <th>Color modes</th>
    <th>Alpha modes</th>
  </tr>
  <tr><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Addition">plus</a></td><td> <a href="http://en.wikipedia.org/wiki/Blend_modes#Difference">difference</a>    </td><td> src      </td></tr>
  <tr><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Subtract">minus</a></td><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Difference">exclusion</a></td><td><a href="">dst</a></td></tr>
  <tr><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Multiply">multiply</a></td><td><a href="">contrast</a></td><td><a href="">src-over</a></td></tr>
  <tr><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Screen">screen</a></td><td><a href="">invert</a></td><td><a href="">dst-over</a></td></tr>
  <tr><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Overlay">overlay</a></td><td><a href="">invert-rgb</a></td><td><a href="">src-in</a></td></tr>
  <tr><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Darken_Only">darken</a></td><td><a href="">grain-merge</a></td><td><a href="">dst-in</a></td></tr>
  <tr><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Lighten_Only">lighten</a></td><td><a href="">grain-extract</a></td><td><a href="">src-out</a></td></tr>
  <tr><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Dodge_and_burn">color-dodge</a></td><td><a href="">hue</a></td><td><a href="">dst-out</a></td></tr>
  <tr><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Dodge_and_burn">color-burn</a></td><td><a href="">saturation</a></td><td><a href="">src-atop</a></td></tr>
  <tr><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Hard_Light">hard-light</a></td><td><a href="">color</a></td><td><a href="">dst-atop</a></td></tr>
  <tr><td><a href="http://en.wikipedia.org/wiki/Blend_modes#Soft_Light">soft-light</a></td><td><a href="">value</a></td><td><a href="">xor</a></td></tr>
</table>

The white cell'd ones are color blending options, while the orange ones are alpha blending options named [Duff-Porter Operations](http://www.imagemagick.org/Usage/compose/#duff-porter).

## A little mathy background

Blend modes can be written as mathematical relationships. A blend mode is a mapping that takes pixels from layers to a new value. Mathematically, for a pixel \\(a\\) in \\(L\_{1}\\) and a pixel \\(b\\) in \\(L\_{2}\\) at the same location as \\(a\\), we have \\(f(a,b) = c\\). These functions differ depending on the composite operation chosen. The function is like: \\(f: \mathbb{R}^2 \rightarrow \mathbb{R}\\) for any rgb value \\(a\_{i}, b\_{i}\in [0,1]\\) where \\(i\\) represents red, blue, or green.

## Color blend modes

The 22 other operations in the table above are mostly covered in the Wikipedia article on [color blend modes](http://en.wikipedia.org/wiki/Blend_modes).

We'll use the following setup to explore `comp-op`s:

![no comp-op]({{site.baseurl}}/images/2015-04-22-comp-ops-for-torque/no-comp-op.png)

### Plus
\\(f(a,b) = \max\\left((0,0,0),\min\\left((a\_{r},a\_{g},a\_{b}) + (b\_{r},b\_{g},b\_{b}),(1,1,1)\\right)\\right)\\)

Sample calculation:
$$\\begin{eqnarray}
\text{cyan} &amp;= \\left(0,1,1\\right) \\newline
\text{magenta} &amp;= \\left(1,0,1\\right)
\\end{eqnarray}$$

$$\\begin{align}
f(cyan,magenta) =&amp; \max\\left((0,0,0),\min\\left((0,1,1) + (1,0,1),(1,1,1)\\right)\\right) \\newline
=&amp; \max\\left((0,0,0),\min\\left((1,1,2),(1,1,1)\\right)\\right) \\newline
=&amp; \max\\left((0,0,0),(1,1,1)\\right) \\newline
=&amp; (1,1,1)
\\end{align}$$

Applying \\(f\\) above on cyan and magenta gives us white or \\((1,1,1)\\) as you can see in the image below.

![plus comp-op]({{site.baseurl}}/images/2015-04-22-comp-ops-for-torque/plus.png)

### Difference
Difference finds the absolute value of the difference between the rgb values of pixels:
\\[f(a,b) = \\|(a\_{r},a\_{g},a\_{b}) - (b\_{r},b\_{g},b\_{b})\\|\\]

For cyan and magenta again, we get:
$$\\begin{align}
f(cyan,magenta) =&amp;\, \\|(0,1,1) - (1,0,1)\\| \\newline
=&amp;\, \\|(-1,1,0)\\| \\newline
=&amp;\, (1,1,0)
\\end{align}$$

Applying \\(f\\) above on cyan and magenta gives us yellow or \\((1,1,0)\\) as you can see below.

![difference comp-op]({{site.baseurl}}/images/2015-04-22-comp-ops-for-torque/difference.png)


### Multiply
Multiply takes the rgb values of each pixel and multiplies them, like element-wise multiplication of two vectors.
\\[f(a,b) = \\left(a\_{r}b\_{r},a\_{g}b\_{g},a\_{b}b\_{b}\\right)\\]

Applying \\(f\\) above on cyan and magenta gives us 
\\[\\left(0\times 1, 1\times 0, 1\times 1\\right) = \left(0,0,1\right),\\]
which is blue, as you can see below

![multiply comp-op]({{site.baseurl}}/images/2015-04-22-comp-ops-for-torque/multiply.png)

## Alpha blending

Blurb about alpha blending:

<blockquote>
The Duff-Porter image composition methods are a traditional set of 12 methods which are very well defined. They are known as Alpha Composition as the images are merged according to some aspect of the image transparency or 'Alpha Channel'. You can find the mathematical definitions for these in the <a href="http://www.w3.org/TR/2009/WD-SVGCompositing-20090430/">SVG Alpha Compositing</a> document.
</blockquote>

Especially useful to visualize the Duff-Porter fill/mask operations: 

![duff porter imagemagick](http://www.imagemagick.org/Usage/compose/tables/montage_triangles.jpg)

## Comp Op Cheat Sheet

<table>
    <tr>
        <th>Use...</th>
        <th>When you want to do...</th>
    </tr>
    <tr>
        <td></td>
        <td></td>
    </tr>
</table>

## Open questions

+ What are the best composite operations for displaying lots of data?
+ Or, given a color scheme, what are the best composite operations that can be used with them?

## Note-worthy reads

+ Stamen's Shawn Allen's [blog post](http://content.stamen.com/trees-cabs-crime_in_venice) about color composting
+ [Mapnik reference](https://github.com/mapnik/mapnik/wiki/Compositing) about compositing, also [here](http://mapnik.org/news/2012/08/27/stamen-compositing-mapnik-v2.1/), and [here](http://mapnik.org/news/2012/04/26/image-filters/)

