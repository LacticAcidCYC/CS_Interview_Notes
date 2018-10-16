# Daily CSS Study Notes

## Useful CSS Snippets

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



### 3. 

HTML:

```html

```

CSS:

```css

```





## Other Notes

## 1. repeat()

### *(1) Definition from MDN:*

The `**repeat()**` [CSS](https://developer.mozilla.org/en-US/docs/Web/CSS) function represents a repeated fragment of the track list, allowing a large number of columns or rows that exhibit a recurring pattern to be written in a more compact form.

This function can be used in the CSS Grid properties [`grid-template-columns`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template-columns) and [`grid-template-rows`](https://developer.mozilla.org/en-US/docs/Web/CSS/grid-template-rows).

### 

### (2) Usage

```css
/*example*/
ul {
  display: grid;
  /*grid-template-columns: 1fr 1fr 1fr 1fr;*/
  grid-template-columns: repeat(4, 1fr);
  grid-gap: 1rem;
}

/*others*/
/* <track-repeat> values */
repeat(4, 1fr)
repeat(4, [col-start] 250px [col-end])
repeat(4, [col-start] 60% [col-end])
repeat(4, [col-start] 1fr [col-end])
repeat(4, [col-start] min-content [col-end])
repeat(4, [col-start] max-content [col-end])
repeat(4, [col-start] auto [col-end])
repeat(4, [col-start] minmax(100px, 1fr) [col-end])
repeat(4, [col-start] fit-content(200px) [col-end])
repeat(4, 10px [col-start] 30% [col-middle] auto [col-end])
repeat(4, [col-start] min-content [col-middle] max-content [col-end])

/* <auto-repeat> values */
repeat(auto-fill, 250px)
repeat(auto-fit, 250px)
repeat(auto-fill, [col-start] 250px [col-end])
repeat(auto-fit, [col-start] 250px [col-end])
repeat(auto-fill, [col-start] minmax(100px, 1fr) [col-end])
repeat(auto-fill, 10px [col-start] 30% [col-middle] 400px [col-end])

/* <fixed-repeat> values */
repeat(4, 250px)
repeat(4, [col-start] 250px [col-end])
repeat(4, [col-start] 60% [col-end])
repeat(4, [col-start] minmax(100px, 1fr) [col-end])
repeat(4, [col-start] fit-content(200px) [col-end])
repeat(4, 10px [col-start] 30% [col-middle] 400px [col-end])
```





## 2. CSS grid

### (1) grid-template-columns & grid-template-rows

```css
grid-template-columns: none | <track-list> | <auto-track-list>
grid-template-rows: none | <track-list> | <auto-track-list>

I. <track-list>: [<line-names>? [ <track-size> | <track-repeat>]] + <line-names>?

II. <track-size>:
(1) <track-breadth>: CSS length | CSS percentage | fr | min-content | max-content | auto
(2) minmax( <inflexible-breadth> , <track-breadth> )
(3) fit-content ( <length-percentage> )

III. <track-repeat>: repeat([ <positive-integer> ] , [ <line-name>? <track-size> ] + <line-names>?)

IV. <line-names>: name the grid lines to make the grid code easier to understand.
repeat(4, [col-start] min-content [col-middle] max-content [col-end])

V. <auto-track-list>

```





















































