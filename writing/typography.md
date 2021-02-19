# PSP starter pack: Thoughts and ideas

One of the things I would have killed for when I came back to doing (serious) web design was a set of guidelines, 
stylesheets and examples of how to use responsive design and typography on the web. This is my attempt at creating 
such a guide. It's far from perfect but it's a starting point. 

I don't hide the fact that I'm opinionated. There are reasons why I choose to do things a given way and I respect 
that you may disagree with me, that's your call. 

### SASS/SCSS versus CSS 
The styles in this guide will be created using SASS 3.4 and the Ruby SASS interpreter. There is a C implementation of
 SASS (libsass) that is supposed to be on par with Ruby SASS. In my experience libsass does not work as well as the 
 Ruby version so I decided it's ok to add the Ruby dependency to the project. 
 
The CSS output of the SASS process will also be available and you can work from the CSS. Just remember that if you 
work from the CSS you are responsible for updating your stylesheets when changes happen. 

### Using Normalize.scss

We need to make sure that our content looks the same in as many browsers as possible. Normalize.css and its SCSS 
version make a good job of smoothing the remaining differences in styles and their application across browsers. It 
has been imported in our `main.scss` stylesheet. 

If you don't want Normalize as part of yout stylesheet, remove the statement `@import 
'partials/normalize/import-now';` from `main.scss`.

### using the Golden Ratio and modular scales

The first thing we'll do is define a modular scale to use in the content. I've chose to use [modular scale]
(http://modularscale.com) by [Scott Kellum](http://scottkellum.com/) and [Tim Brown](http://tbrown.org/) as a way to 
make it easier to create a scale that works across sizes and accross devices. 

There is also a SASS plugin (will work with Compass as well as vanilla SASS). To install in vanilla SASS follow these
 instructions:

* Download the latest version modular scale plugin from [Github](https://github.com/Team-Sass/modular-scale/releases/latest)
* Unzip the content to your project (I used the `src/sass` directory)
* In your main SCSS file `@import 'modular-scale/modular-scale`

For example, to define the font sizes of the 6 heading elements (h1 through h6), we could do something like this:

```scss
h1 {
  font-size: ms(4);
  line-height: 1.2;
}
h2 {
  font-size: ms(3);
  line-height: 1.1;
}
h3 {
  font-size: ms(2);
  line-height: 1.1;
}
h4 {
  font-size: ms(1);
  line-height: 1.1;
  text-transform: uppercase;
  word-spacing: .2em;
}
h5 {
  font-size: ms(1);
  line-height: 1.1;
  text-transform: uppercase;
}
h6 {
  font-size: ms(1);
  line-height: 1.1;
}
```

and the resulting CSS looks like this:

```css
h1 {
  font-size: 6.8541em;
  line-height: 1.2;
}

h2 {
  font-size: 4.23607em;
  line-height: 1.1;
}

h3 {
  font-size: 2.61803em;
  line-height: 1.1;
}

h4 {
  font-size: 1.61803em;
  line-height: 1.1;
  text-transform: uppercase;
  word-spacing: .2em;
}

h5 {
  font-size: 1.61803em;
  line-height: 1.1;
  text-transform: uppercase;
}

h6 {
  font-size: 1.61803em;
  line-height: 1.1;
}
```
The one thing I don't like about the sizes we get using the Golden Ratio is that they are too big. The Modular Scale 
makes available different scales. If we use the change the scale to 15:16, also known as a minor second scale. The 
values of our headers will also change.  To make these changes, add the following variables to your stylesheet, or to
 your variables file:
 
```scss
$ms-base: 1em;
$ms-ratio: 1.067;
```

In this scales the headings become harder to differentiate:

```css
h1 {
  font-size: 1.29616em;
  line-height: 1.2;
}

h2 {
  font-size: 1.21477em;
  line-height: 1.1;
}

h3 {
  font-size: 1.13849em;
  line-height: 1.1;
}

h4 {
  font-size: 1.067em;
  line-height: 1.1;
  text-transform: uppercase;
  word-spacing: .2em;
}

h5 {
  font-size: 1.067em;
  line-height: 1.1;
  text-transform: uppercase;
  word-spacing: .2em;
}

h6 {
  font-size: 1.067em;
  line-height: 1.1;
}
```

We'll try one more scale, the major third scale (1.25). We'll change the variables to those below:

```scss
$ms-base: 1em;
$ms-ratio: 1.25;
```

```scss
h1 {
  font-size: ms(6);
  line-height: 1.2;
}
h2 {
  font-size: ms(5);
  line-height: 1.1;
}
h3 {
  font-size: ms(4);
  line-height: 1.1;
}
h4 {
  font-size: ms(3);
  line-height: 1.1;
}
h5 {
  font-size: ms(2);
  line-height: 1.1;
}
h6 {
  font-size: ms(1);
  line-height: 1.1;
}
```

And the resulting CSS

```css
h1 {
  font-size: 3.8147em;
  line-height: 1.2;
}

h2 {
  font-size: 3.05176em;
  line-height: 1.1;
}

h3 {
  font-size: 2.44141em;
  line-height: 1.1;
}

h4 {
  font-size: 1.95313em;
  line-height: 1.1;
}

h5 {
  font-size: 1.5625em;
  line-height: 1.1;
}

h6 {
  font-size: 1.25em;
  line-height: 1.1;
}
```

There are other scales available. You can play with them to find the scale that suits your fonts, your design and 
your tastes the best

We'll pair the modular scale with media queries to change the sizes for smaller form factors. 

Modular scale includes functions for a number of classic design and musical scale ratios. You can add your own ratios as well.

<table>

  <tr><th>Function</th><th>Ratio</th><th>Decimal value</th></tr>

  <tr><td>$phi</td><td>1:1.618</td><td>1.618</td></tr>
  <tr><td>$golden</td><td>1:1.618</td><td>1.618</td></tr>
  <tr><td>$double-octave</td><td>1:4</td><td>4</td></tr>
  <tr><td>$major-twelfth</td><td>1:3</td><td>3</td></tr>
  <tr><td>$major-eleventh</td><td>3:8</td><td>2.667</td></tr>
  <tr><td>$major-tenth</td><td>2:5</td><td>2.5</td></tr>
  <tr><td>$octave</td><td>1:2</td><td>2</td></tr>
  <tr><td>$major-seventh</td><td>8:15</td><td>1.875</td></tr>
  <tr><td>$minor-seventh</td><td>9:16</td><td>1.778</td></tr>
  <tr><td>$major-sixth</td><td>3:5</td><td>1.667</td></tr>
  <tr><td>$minor-sixth</td><td>5:8</td><td>1.6</td></tr>
  <tr><td>$fifth</td><td>2:3</td><td>1.5</td></tr>
  <tr><td>$augmented-fourth</td><td>1:√2</td><td>1.414</td></tr>
  <tr><td>$fourth</td><td>3:4</td><td>1.333</td></tr>
  <tr><td>$major-third</td><td>4:5</td><td>1.25</td></tr>
  <tr><td>$minor-third</td><td>5:6</td><td>1.2</td></tr>
  <tr><td>$major-second</td><td>8:9</td><td>1.125</td></tr>
  <tr><td>$minor-second</td><td>15:16</td><td>1.067</td></tr>

</table>

### Defining a list of variable to use

For a long time SASS was the only game in town when it came to using variables or variable-like constructs for your 
stylesheets. CSS caught up with a more dynamic concept of variables. 

I love CSS variables but, for building a framework SASS variables will work best. If necessary we can convert some of
 the SASS variables discussed below into CSS variables to use after transformation. 

Some variables to think about:

* Colors: We can create variables for colors in the Material Design Palette, for example, and then use SASS functions
 to create lighter and darker versions of each color without having to do the calculations manually. 
* Default font sizes: 

## Fonts loading and use

Rather than let font loading remain atth e mercy of the network we'll use Font Face Observer to dynamically load the 
fonts and swap them when ready. This may cause a little user cognitive dissonance but it's better than the 
alternatives of browsers taking forever or not loading the font.  

### setting up fonts

We'll use the following mixin to create our `@font-face` declarations

```scss
@mixin font-declaration(  $font-family,
                          $font-file-name,
                          $weight:normal, 
                          $style:normal) {
  @font-face {
    font-family: '#{$font-family}';
    src: url('#{$font-file-name}.eot');
    src: url('#{$font-file-name}.eot?#iefix') format('embedded-opentype'),
    url('#{$font-file-name}.woff2') format('woff2'),
    url('#{$font-file-name}.woff') format('woff'),
    url('#{$font-file-name}.ttf') format('truetype'),
      // working with myFonts requires the svg file 
      // to be formated like this
      // url('#{$font-file-name}.svg#wf') format('svg');
    url('#{$font-file-name}.svg##{$font-family}') format('svg');
    font-weight: $weight;
    font-style: $style;
  }
}
```

And use them as follows:

```scss
// add font-declarations
@include font-declaration('notomono_regular', '../fonts/notomono-regular-webfont', normal, normal);
@include font-declaration('notosans_regular', '../fonts/notosans-regular-webfont', normal, normal);
@include font-declaration('notosans_bold', '../fonts/notosans-bold-webfont', 700 , normal);
@include font-declaration('notosans_italic', '../fonts/notosans-italic-webfont', normal , italic);
@include font-declaration('notosans_bolditalic', '../fonts/notosans-bolditalic-webfont', 700 , italic);
```

to create these `@font-face` declarations:

```css

@font-face {
  font-family: "notomono_regular";
  src: url("../fonts/notomono-regular-webfont.eot");
  src: url("../fonts/notomono-regular-webfont.eot?#iefix") format("embedded-opentype"), 
       url("../fonts/notomono-regular-webfont.woff2") format("woff2"), 
       url("../fonts/notomono-regular-webfont.woff") format("woff"), 
       url("../fonts/notomono-regular-webfont.ttf") format("truetype"), 
       url("../fonts/notomono-regular-webfont.svg#notomono_regular") format("svg");
  font-weight: normal;
  font-style: normal;
}
@font-face {
  font-family: "notosans_regular";
  src: url("../fonts/notosans-regular-webfont.eot");
  src: url("../fonts/notosans-regular-webfont.eot?#iefix") format("embedded-opentype"), 
       url("../fonts/notosans-regular-webfont.woff2") format("woff2"), 
       url("../fonts/notosans-regular-webfont.woff") format("woff"), 
       url("../fonts/notosans-regular-webfont.ttf") format("truetype"), 
       url("../fonts/notosans-regular-webfont.svg#notosans_regular") format("svg");
  font-weight: normal;
  font-style: normal;
}
@font-face {
  font-family: "notosans_bold";
  src: url("../fonts/notosans-bold-webfont.eot");
  src: url("../fonts/notosans-bold-webfont.eot?#iefix") format("embedded-opentype"), 
       url("../fonts/notosans-bold-webfont.woff2") format("woff2"), 
       url("../fonts/notosans-bold-webfont.woff")  format("woff"), 
       url("../fonts/notosans-bold-webfont.ttf") format ("truetype"), 
       url("../fonts/notosans-bold-webfont.svg#notosans_bold") format("svg");
  font-weight: 700;
  font-style: normal;
}
@font-face {
  font-family: "notosans_italic";
  src: url("../fonts/notosans-italic-webfont.eot");
  src: url("../fonts/notosans-italic-webfont.eot?#iefix") format("embedded-opentype"), 
       url("../fonts/notosans-italic-webfont.woff2") format("woff2"), 
       url("../fonts/notosans-italic-webfont.woff") format("woff"), 
       url("../fonts/notosans-italic-webfont.ttf") format("truetype"), 
       url("../fonts/notosans-italic-webfont.svg#notosans_italic") format("svg");
  font-weight: normal;
  font-style: italic;
}
@font-face {
  font-family: "notosans_bolditalic";
  src: url("../fonts/notosans-bolditalic-webfont.eot");
  src: url("../fonts/notosans-bolditalic-webfont.eot?#iefix") format("embedded-opentype"), 
       url("../fonts/notosans-bolditalic-webfont.woff2") format("woff2"), 
       url("../fonts/notosans-bolditalic-webfont.woff") format("woff"), 
       url("../fonts/notosans-bolditalic-webfont.ttf") format("truetype"), 
       url("../fonts/notosans-bolditalic-webfont.svg#notosans_bolditalic") format("svg");
  font-weight: 700;
  font-style: italic;
}
```

Note that we create 4 `@font-face` declarations for our notosans: 

* One for regular
* One for bold
* One for italics 
* One for bolditalics

We do this to prevent faux bold and faux italics that we'll discuss in more details later.  

### Loading fonts with Font Face Observer

The first thing to do is to modify our SCSS file to add a plain body tag with our backup fonts. We also add a class 
of `.fonts-loaded` class to the body declaration that contains our web font declaration for the body tag. 

```scss
body {
  font-family: Verdana, sans-serif;
}

.fonts-loaded body {
  font-family: "notosans_regular", Verdana, sans-serif;
}
```

In Javascript we'll create add a script tag linking to `fontfaceobserver.js`

```html
<script src="fontfaceobserver.js"></script> 
```

To load a single font we create variables to hold a `FontFaceObserver` with the name of the fonts we defined in the 
`@font-face` declaration and a reference to 'document.documentElement`.

We add a class to the document to indicate tht fonts are loading. 

We load the font and that creates a promise. If the promise succeeds then we remove the `font-loading` class and 
replace it with `fonts-loaded`.
 
If the font fails to load the promise will reject and jump to the catch statement. This will replace the 
`font-loading` class with `font-failed`. We can use this failed class to 

```javascript
var font = new FontFaceObserver('notosans-regular');
var html = document.documentElement;

html.classList.add('fonts-loading');

font.load().then(function () {
  html.classList.remove('fonts-loading');
  html.classList.add('fonts-loaded');
}).catch(function () {
  html.classList.remove('fonts-loading');
  html.classList.add('fonts-failed');
});
```


```javascript
var mono = new FontFaceObserver('notomono_regular');
var sans = new FontFaceObserver('notosans_regular');
var italic = new FontFaceObserver('notosans_italic');
var bold = new FontFaceObserver('notosans_bold');
var bolditalic = new FontFaceObserver('notosans_bolditalic');

var html = document.documentElement;

html.classList.add('fonts-loading');

Promise.all([
  mono.load(),
  sans.load(),
  bold.load(),
  italic.load(),
  bolditalic.load()
]).then(function() {
  html.classList.remove('fonts-loading');
  html.classList.add('fonts-loaded');
  console.log('Fonts have loaded.');
}).catch(function(){
  html.classList.remove('fonts-loading');
  html.classList.add('fonts-failed');
  console.log('One or more fonts failed to load')
});

```

### Preventing faux fonts

```scss
strong, b {
  font-family: "noto_sansbold";
}

em, i {
  font-family: "noto_sansbold_italic";
}
```

## Taking advantage of SASS to play with colors

http://dudleystorey.github.io/thenewdefaults/

https://material.google.com/style/color.html

https://www.materialpalette.com/

http://materializecss.com/color.html

## defaults in the body element

## Layouts: The Next Generation

### Columns

### Flexbox

### Grids

## Typography DRY tools

### Dropcaps

### Shapes

## Typesetting using utility-opentype

http://utility-opentype.kennethormandy.com/


