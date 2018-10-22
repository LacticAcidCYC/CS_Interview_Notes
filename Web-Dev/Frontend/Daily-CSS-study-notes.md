# Daily CSS Study Notes

## -Useful CSS Snippets

[Learning Website](https://30-seconds.github.io/30-seconds-of-css/)

### 1. Bouncing loader

HTML:

```html
<div class="bouncing-loader">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
</div>
```

CSS:

```css
@keyframes bouncing-loader {
    0% {
        opacity: 1;
        transform: translateY(0);
    }
    100% {
        opacity: 0.1;
        transform: translateY(-1rem);
    }
}

.bouncing-loader {
    display: flex;
    justify-content: center;
}

.bouncing-loader > div {
    width: 1rem;
    height: 1rem;
    margin: 3rem 0.2rem;
    background: #8385aa;
    border-radius: 50%;
    animation: bouncing-loader .6s infinite alternate;
}

.bouncing-loader > div:nth-child(2) {
    animation-delay: .1s;
}

.bouncing-loader > div:nth-child(3) {
    animation-delay: .2s;
}

.bouncing-loader > div:nth-child(4) {
    animation-delay: .3s;
}

.bouncing-loader > div:nth-child(5) {
    animation-delay: .4s;
}

.bouncing-loader > div:nth-child(6) {
    animation-delay: .5s;
}
```

[jsfiddle](http://jsfiddle.net/LacticAcidCYC/ab8g3o2d/)

#### Explanation

**Note: `1rem` is usually `16px`.**

(1) `@keyframes` defines an animation that has two states, where the element changes `opacity` and is translated up on the 2D plane using `transform: translateY()`.

(2) `.bouncing-loader` is the parent container of the bouncing circles and uses `display: flex` and `justify-content: center` to position them in the center.

(3) `.bouncing-loader > div`, targets the three child `div`s of the parent to be styled. The `div`s are given a width and height of `1rem`, using `border-radius: 50%` to turn them from squares to circles.

(4) `margin: 3rem 0.2rem` specifies that each circle has a top/bottom margin of `3rem` and left/right margin of `0.2rem` so that they do not directly touch each other, giving them some breathing room.

(5) `animation` is a shorthand property for the various animation properties: `animation-name`, `animation-duration`, `animation-iteration-count`, `animation-direction` are used.

(6) `nth-child(n)` targets the element which is the nth child of its parent.

(7) `animation-delay` is used on the second and third `div` respectively, so that each element does not start the animation at the same time.



### 2. Loader Practice

HTML:

```html
<div class="loader">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
</div>
```

CSS:

```css
@keyframes loader {
    from {
        opacity: 1;
        transform: scale(1, 1);
    }

    to {
        opacity: 0.2;
        transform: scale(0);
    }
}

.loader {
    display: flex;
    justify-content: center;
}

.loader > div {
    width: 1.5rem;
    height: 1.5rem;
    margin: 3rem 0.2rem;
    background: #8385aa;
    border-radius: 50%;
    animation: loader 0.6s ease-in-out infinite alternate;
}

.loader > div:nth-child(2) {
    animation-delay: .15s;
}

.loader > div:nth-child(3) {
    animation-delay: .3s;
}

.loader > div:nth-child(4) {
    animation-delay: .45s;
}
```

[jsfiddle](http://jsfiddle.net/LacticAcidCYC/dbcz0gxL)



### 3. Box-sizing reset

Usage: Resets the box-model so that `width`s and `height`s are not affected by their `border` or `padding`.

HTML:

```html
<div class="box">border-box</div>
<div class="box content-box">content-box</div>
```

CSS:

```css
html {
  box-sizing: border-box;
}
*,
*::before,
*::after {
  box-sizing: inherit;
}

.box {
  display: inline-block;
  width: 150px;
  height: 150px;
  padding: 10px;
  background: tomato;
  color: white;
  border: 10px solid red;
}

.content-box {
  box-sizing: content-box;
}
```

[codepen](https://codepen.io/lacticacidcyc/pen/JmBrpz)

#### Explanation

(1) `box-sizing: border-box` makes the addition of `padding` or `border`s not affect an element's `width` or `height`.

(2) `box-sizing: inherit` makes an element respect its parent's `box-sizing` rule.



### 4. Circle

Creates a circle shape with pure CSS.

HTML:

```html
<div class="circle"></div>
```

CSS:

```css
.circle {
  background: tomato;
  width: 3rem;
  height: 3rem;
  border-radius: 50%;
}
```

[codepen](https://codepen.io/lacticacidcyc/pen/NOBaJa)

#### Explanation

`border-radius: 50%` curves the borders of an element to create a circle.

Since a circle has the same radius at any given point, the `width` and `height` must be the same. Differing values will create an ellipse.



### 5. Clearfix

Ensures that an element self-clears its children.

**Especially when using Float to set a picture on the left or right side. If the text next to it is shorter than the picture, without clearfix would cause the problem that the elements below are also next to the floated picture in the same block context.**

*Note: This is only useful if you are still using float to build layouts. Please consider using a modern approach with flexbox layout or grid layout.*

HTML:

```html
<div class="clearfix">
  <div class="floated">float a</div>
  <div class="floated">float b</div>
  <div class="floated">float c</div>
</div>
<div class="floated">float d</div>
<div class="floated">float e</div>
<div class="floated">float f</div>
```

CSS:

```css
.clearfix::after {
  content: '';
  display: block;
  clear: both;
}

.floated {
  float: left;
}
```

[codepen](https://codepen.io/lacticacidcyc/pen/mzjBNO)

#### Explanation

(1) `.clearfix::after` defines a pseudo-element.

(2) `content: ''` allows the pseudo-element to affect layout.

(3) `clear: both` indicates that the left, right or both sides of the element cannot be adjacent to earlier floated elements within the same block formatting context.

####  

















































