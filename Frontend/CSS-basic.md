

# CSS Basic Knowledge

## 1. CSS Specificity

Three things that control which css rule applies to a given html element:

- [*Specificity Calculations*](#specificity-calculations)
- [*Inheritance*](#inheritance)
- [*The Cascade*](#cascade)

### <span id="specificity-calculations">Specificity Calculations</span>

Specificity is calculated by counting various components of your css and expressing them in a form (a,b,c,d):

- *Element, Pseudo Element*: d = 1 –> (0,0,0,1)
- *Class, Pseudo class, Attribute*: c = 1 –> (0,0,1,0)
- *Id*: b = 1 -> (0,1,0,0)
- *Inline Style*: a = 1 –> (1,0,0,0)

**An id is more specific than a class is more specific than an element.**

You calculate specificity by counting each of the above and adding 1 to either a,b,c, or d. It’s also important to note that 0,0,1,0 is more specific than 0,0,0,15.

More examples:

- p: 1 element – (0,0,0,1)
- div: 1 element – (0,0,0,1)
- \#sidebar: 1 id – (0,1,0,0)
- div#sidebar: 1 element, 1 id – (0,1,0,1)
- div#sidebar p: 2 elements, 1 id – (0,1,0,2)
- div#sidebar p.bio: 2 elements, 1 class, 1 id – (0,1,1,2)

### <span id="inheritance">Inheritance</span>

Elements inherit styles from their parent container. If you set the body tag to use color: red then the text for all elements inside the body will also be red unless otherwise specified.

Not all css properties are inherited, though. For example *margins* and *paddings* are non-inherited properties. If you set a margin or padding on a div, the paragraphs inside that div do not inherit the margin and padding you set on the div. The paragraph will use the default browser margin and padding until you declare otherwise.

You can explicitly set a property to inherit styles from it’s parent container, though. For example you could declare:

```css
p {
    margin: inherit;
    padding: inherit;
}
```

and your paragraph would then inherit both from it’s containing element.

### <span id="cascade">The Cascade</span>

At the highest level the cascade is what controls all css precedence and works as follows.

1. Find all css declarations that apply to the element and property in question.
2. Sort by origin and weight. Origin refers to the source of the declaration (author styles, user styles, browser defaults) and weight refers to the importance of the declaration. (author has more weight than user which has more weight than default. !importance has more weight than normal declarations)
3. Calculate specificity
4. If two rules are equal in all of the above, the one declared last wins. CSS embedded in the html always come after external stylesheets regardless of the order in the html

\#3 above is likely the one you’ll need to pay attention to most. With #2 just understand that your styles will override how a user sets their browser unless they set their rules to be important.

**Also realize that your styles will override the browser defaults, but those defaults do exist and is often what leads to cross browser issues. Using a reset file like [Eric Meyer’s CSS Reset](http://meyerweb.com/eric/thoughts/2007/05/01/reset-reloaded/) or [Yahoo’s YUI Reset CSS](http://developer.yahoo.com/yui/reset/) helps take the default styles out of the equation.**

**Reference**: [vanseodesign](http://vanseodesign.com/css/css-specificity-inheritance-cascaade/)