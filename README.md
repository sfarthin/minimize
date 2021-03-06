[![Build Status][status]](https://travis-ci.org/Moveo/minimize)
[![NPM version][npmimgurl]](http://npmjs.org/package/minimize)
[![Coverage Status][coverage]](http://coveralls.io/r/Moveo/minimize?branch=master)

[status]: https://travis-ci.org/Moveo/minimize.png?branch=master
[npmimgurl]: https://badge.fury.io/js/minimize.png
[coverage]: http://coveralls.io/repos/Moveo/minimize/badge.png?branch=master

# HTML minifier

Minimize is a HTML minifier based on the node-htmlparser. This depedency will
ensure output is solid and correct. Minimize is focussed on HTML5 and will not
support older HTML drafts. It is not worth the effort and the web should move
forward. Currently, HTML minifier is only usuable server side. Client side
minification will be added in a future release.

## Features

- fast and stable html minification
- highly configurable
- can distinguish conditional IE comments
- build on the foundations of htmlparser2

## Upcoming in release 1.0

- command line usage support

## Upcoming in release 2.0

- minification of inline javascript by [square](https://github.com/observing/square)
- client side minification support

## Usage

To get the minified content make sure to provide a callback. Optional an options
object can be provided. All options are listed below and `false` per default.

```javascript
var Minimize = require('minimize')
  , minimize = new Minimize({
      empty: true,        // DO NOT remove empty attributes
      cdata: true,        // DO NOT strip CDATA from scripts
      comments: true,     // DO NOT remove comments
      ssi: true,          // DO NOT remove Server Side Includes (i.e. <!--#include virtual="../quote.txt" -->)
      conditionals: true, // DO NOT remove conditional internet explorer comments
      spare: true,        // DO NOT remove redundant attributes
      quotes: true        // DO NOT remove arbitrary quotes
    });

minimize.parse(content, function (error, data) {
  console.log(data);
});
```

## Options

**Empty**

Empty attributes can usually be removed, by default all are removed, excluded
HTML5 _data-*_ and microdata attributes. To retain empty elements regardless
value, do:

```javascript
var Minimize = require('minimize')
  , minimize = new Minimize({ empty: true });

minimize.parse(
  '<h1 id=""></h1>',
  function (error, data) {
    // data output: <h1 id=""></h1>
  }
);
```

**CDATA**

CDATA is only required for HTML to parse as valid XML. For normal webpages this
is rarely the case, thus CDATA around javascript can be omitted. By default
CDATA is removed, if you would like to keep it, pass true:

```javascript
var Minimize = require('minimize')
  , minimize = new Minimize({ cdata: true });

minimize.parse(
  '<script type="text/javascript">\n//<![CDATA[\n...code...\n//]]>\n</script>',
  function (error, data) {
    // data output: <script type=text/javascript>//<![CDATA[\n...code...\n//]]></script>
  }
);
```

**Comments**

Comments inside HTML are usually beneficial while developing. Hiding your
comments in production is sane, safe and will reduce data transfer. If you
ensist on keeping them, for instance to show a nice easter egg, set the option
to true.

```javascript
var Minimize = require('minimize')
  , minimize = new Minimize({ comments: true });

minimize.parse(
  '<!-- some HTML comment -->\n     <div class="slide nodejs">',
  function (error, data) {
    // data output: <!-- some HTML comment --><div class="slide nodejs">
  }
);
```

**Conditionals**

Conditional comments only work in IE, and are thus excellently suited to give
special instructions meant only for IE. Minimize can be configured to retain
these comments. But since the comments are only working until IE9 (inclusive)
the default is to remove the conditionals.

```javascript
var Minimize = require('minimize')
  , minimize = new Minimize({ conditionals: true });

minimize.parse(
  "<!--[if ie6]>Cover microsofts' ass<![endif]-->\n<br>",
  function (error, data) {
    // data output: <!--[if ie6]>Cover microsofts' ass<![endif]-->\n<br>
  }
);
```


**Spare**

Spare attributes are of type boolean of which the value can be omitted in HTML5.
To keep attributes intact for support of older browsers, supply:

```javascript
var Minimize = require('minimize')
  , minimize = new Minimize({ spare: true });

minimize.parse(
  '<input type="text" disabled="disabled"></h1>',
  function (error, data) {
    // data output: <input type=text disabled=disabled></h1>
  }
);
```

**Qoutes**

Quotes are always added around attributes that have spaces or an equal sign in
their value. But if you require quotes around all attributes, simply pass
quotes:true, like below.

```javascript
var Minimize = require('minimize')
  , minimize = new Minimize({ quotes: true });

minimize.parse(
  '<p class="paragraph" id="title">\n    Some content\n  </p>',
  function (error, data) {
    // data output: <p class="paragraph" id="title">Some content</p>
  }
);
```

## Tests

Tests can be easily run by using either of the following commands. Travis.ci is
used for continous integration.

```bash
make test
make test-watch
npm test
```

## Benchmarks


## Credits
Minimize is influenced by the [HTML minifier][kangax] of kangax. This module
parses the DOM as string as opposes to an object. However, retaining flow is more
diffucult if the DOM is parsed sequentially. Minimize is not client-side ready.
Kangax minifier also provides some additional options like linting. Minimize
will retain strictly to the business of minifying. Minimize is already used in
production by [Nodejitsu][nodejitsu].

[node-htmlparser][fb55] of fb55 is used to create an object representation
of the DOM.

[kangax]: https://github.com/kangax/html-minifier/
[fb55]: https://github.com/fb55/node-htmlparser/
[nodejitsu]: http://www.nodejitsu.com/
