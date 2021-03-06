# gatsby-remark-embed-snippet

Embeds the contents of specified files within Prism-formatted HTML blocks.

## Overview

### Embedding Files

For example, given the following project directory structure:

```
./examples/
├── sample-javascript-file.js
├── sample-html-file.html
```

The following markdown syntax could be used to embed the contents of these
files:

```md
# Sample JavaScript

`embed:sample-javascript-file.js`

# Sample HTML

`embed:sample-html-file.html`
```

The resulting HTML for the above markdown would look something like this:

```html
<h1>Sample JavaScript</h1>
<div class="gatsby-highlight">
  <pre class="language-jsx">
    <code>
      <!-- Embedded content here ... -->
    </code>
  </pre>
</div>

<h1>Sample HTML</h1>
<div class="gatsby-highlight">
  <pre class="language-html">
    <code>
      <!-- Embedded content here ... -->
    </code>
  </pre>
</div>
```

### Highlighting Lines

You can also specify specific lines for Prism to highlight using
`highlight-line` and `highlight-next-line` comments. You can also specify a
range of lines to highlight, relative to a `highlight-range` comment.

#### JavaScript example

```js
import React from "react"
import ReactDOM from "react-dom"

const name = "Brian" // highlight-line

ReactDOM.render(
  <div>
    {/* highlight-range{1-3} */}
    <h1>Hello, ${name}!</h1>
  </div>,
  document.getElementById("root")
)
```

#### CSS example

```css
html {
  /* highlight-range{1-2} */
  height: 100%;
  width: 100%;
}

* {
  box-sizing: border-box; /* highlight-line */
}
```

#### HTML example

```html
<html>
  <body>
    <h1>highlight me</h1>
    <!-- highlight-line -->
    <p>
      <!-- highlight-next-line -->
      And me
    </p>
  </body>
</html>
```

#### YAML example

```yaml
foo: "highlighted" # highlight-line
bar: "not highlighted"
# highlight-range{1-2}
baz: "highlighted"
quz: "highlighted"
```

### Hide Lines

It's also possible to specify a range of lines to be hidden.

#### JavaScript example

```js
// hide-range{1-2}
import React from "react"
import ReactDOM from "react-dom"

function App() {
  return (
    <div className="App">
      <ul>
        <li>Not hidden</li>
        <li>Not hidden</li>
        {/* hide-range{1-2} */}
        <li>Hidden</li>
        <li>Hidden</li>
        {/* hide-next-line */}
        <li>Hidden</li>
      </ul>
    </div>
  )
}

// hide-range{1-2}
const rootElement = document.getElementById("root")
ReactDOM.render(<App />, rootElement)
```

Will produce something like this:

```js
function App() {
  return (
    <div className="App">
      <ul>
        <li>Not hidden</li>
        <li>Not hidden</li>
      </ul>
    </div>
  )
}
```

## Installation

`npm install --save gatsby-remark-embed-snippet`

## How to use

**Important**: This module must appear before `gatsby-remark-prismjs` in your
plugins array, or the markup will have already been transformed into a code
block and this plugin will fail to detect it and inline the file.
For further information about its options, visit the `gatsby-remark-prismjs`
[README](https://www.gatsbyjs.org/packages/gatsby-remark-prismjs/).

```js
// In your gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-transformer-remark`,
      options: {
        plugins: [
          {
            resolve: "gatsby-remark-embed-snippet",
            options: {
              // Example code links are relative to this dir.
              // eg examples/path/to/file.js
              directory: `${__dirname}/examples/`,
            },
          },
          {
            resolve: `gatsby-remark-prismjs`,
            options: {
              // Class prefix for <pre> tags containing syntax highlighting;
              // defaults to 'language-' (eg <pre class="language-js">).
              // If your site loads Prism into the browser at runtime,
              // (eg for use with libraries like react-live),
              // you may use this to prevent Prism from re-processing syntax.
              // This is an uncommon use-case though;
              // If you're unsure, it's best to use the default value.
              classPrefix: "language-",
              // This is used to allow setting a language for inline code
              // (i.e. single backticks) by creating a separator.
              // This separator is a string and will do no white-space
              // stripping.
              // A suggested value for English speakers is the non-ascii
              // character '›'.
              inlineCodeMarker: null,
              // This lets you set up language aliases.  For example,
              // setting this to '{ sh: "bash" }' will let you use
              // the language "sh" which will highlight using the
              // bash highlighter.
              aliases: {},
              // This toggles the display of line numbers globally alongside the code.
              // To use it, add the following line in gatsby-browser.js
              // right after importing the prism color scheme:
              //  `require("prismjs/plugins/line-numbers/prism-line-numbers.css");`
              // Defaults to false.
              // If you wish to only show line numbers on certain code blocks,
              // leave false and use the {numberLines: true} syntax.
              showLineNumbers: false,
              // If setting this to true, the parser won't handle and highlight inline
              // code used in markdown i.e. single backtick code like `this`.
              noInlineHighlight: false,
            },
          },
        ],
      },
    },
  ],
}
```
