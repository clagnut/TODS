# Typography and Opentype Default Stylesheet (TODS)
 
Initiated following a conversation at [CSS Day](https://cssday.nl/2024/speakers#roel) with [Roel Nieskens](https://pixelambacht.nl/) and his [Mildly Opinionated Prose Styles (MOPS)](https://github.com/RoelN/mops). 

The idea is to set sensible typographic defaults for use on prose (a column of text). The main principle is that it can be used as starting point for all projects, so doesn't include design-specific aspects such as font choice, type scale and layout (including how you might like to set the line-length).

Within the styles is mildly opinionated best practice, which will help set suitable styles should you forget. This means you can also use the style sheet as a checklist, even if you don't want to implement it as-is.

TODS uses OpenType features extensively and variable font axes where available. It makes full use of the cascade to set sensible defaults high up, with overrides applied further down. It also contains some handy utility classes.

You can apply the `TODS.css` stylesheet in its entirety, as its full functionality relies on progressive enhancement within both browsers and fonts. Anything that is not supported will safely be ignored. The only possible exceptions to this are sub/superscripts and application of a grade axis in dark mode, as these are font-specific and could behave unexpectedly depending on the capability of the font.

## Walkthrough of the TODS.css stylesheet

### 1. Reset

Based on Andy Bell's [more modern CSS reset](https://piccalil.li/blog/a-more-modern-css-reset/). The typographic rules in the reset are extracted here. You might like to apply the other rules too.

```
html {
  -moz-text-size-adjust: none;
  -webkit-text-size-adjust: none;
  text-size-adjust: none;
}
```

Prevent font size inflation when rotating from portrait to landscape. The [best explainer for this is by Kilian](https://kilianvalkhof.com/2022/css-html/your-css-reset-needs-text-size-adjust-probably/). He also explains why we still need those ugly prefixes too.

```
body, h1, h2, h3, h4, h5, h6, address, p, hr, pre, blockquote, ol, ul, li, dl, dt, dd, figure, figcaption, div, table, caption, form, fieldset	{
  margin: 0;
}
```

Remove default margins in favour of better control in authored CSS.

```
input,
button,
textarea,
select {
  font-family: inherit;
  font-size: inherit;
}
```

Inherit fonts for inputs and buttons.

### 2. Web fonts

Use modern variable font syntax so that only supporting browsers get the variable font. Others will get generic fallbacks.

```
@font-face {
	font-family: 'Literata';
	src:	url('/fonts/Literata-var.woff2') format('woff2') tech(variations),
			url('/fonts/Literata-var.woff2') format('woff2-variations');
	font-weight: 1 1000;
	font-stretch: 50% 200%;
	font-style: normal;
	font-display: fallback;
}
```

Include full possible weight range to avoid unintended synthesis of variable fonts with a weight axis. Same applies to stretch range for variable fonts with a width axis.

For main body fonts, use `fallback` for how the browser should behave while the webfont is loading. This gives the font an extremely small block period and a short swap period, providing the best chance for text to render.

```
@font-face {
	font-family: 'Literata';
	src:	url('/fonts/Literata-Italic-var.woff2') format('woff2') tech(variations),
			url('/fonts/Literata-Italic-var.woff2') format('woff2-variations');
	font-weight: 1 1000;
	font-stretch: 50% 200%;
	font-style: italic;
	font-display: swap;
}
```

For italics use `swap` for an extremely small block period and an infinite swap period. This means italics can be synthesised and swapped in once loaded.

```
@font-face {
	font-family: 'Plex Sans';
	src:	url('/fonts/Plex-Sans-var.woff2') format('woff2') tech(variations),
			url('/fonts/Plex-Sans-var.woff2') format('woff2-variations');
	font-weight: 1 1000;
	font-stretch: 50% 200%;
	font-style: normal;
	font-display: fallback;
	size-adjust:105%; /* make monospace fonts slightly bigger to match body text. Adjust to suit - you might need to make them smaller */
}
```

When monospace fonts are used inline with text fonts, they often need tweaking to appear balanced in terms of size. Use `size-adjust` to do this without affecting reported font size and associated units such as `em`.

### 3. Global defaults

Set some sensible defaults that can be used throughout the whole web page. Override these where you need to through the magic of the cascade.

```
body {
	line-height: 1.5;
	text-decoration-skip-ink: auto;

	font-optical-sizing: auto;
	font-variant-ligatures: common-ligatures no-discretionary-ligatures no-historical-ligatures contextual;
	font-kerning: normal;
}
```

Set a nice legible line height that gets inherited. The `font-` properties are set too default CSS and OpenType settings, however they are  still worth setting specifically just in case.

```
button, input, label { 
	line-height: 1.1; 
}
```

Set shorter line heights on interactive elements. We’ll do the same for headings later on.

### 4. Block spacing 

Reinstate block margins we removed in the reset section. We’re setting consistent spacing based on font size on primary elements within ‘flow’ contexts. The entire ‘prose’ area is a flow context, but so might other parts of the page. For more details on the ‘flow’ utility see Andy Bell’s [favourite three lines of CSS](https://piccalil.li/blog/my-favourite-3-lines-of-css).

```
.flow > * + * {
  margin-block-start: var(--flow-space, 1em);
}
```

Rule says that every direct sibling child element of `.flow` has `margin-block-start` added to it. The `>` combinator is added to prevent margins being added recursively.

```
.prose {
  --flow-space: 1.5em;
}
```

Set generous spacing between primary block elements (in this case it’s the same as the line height). You could also choose a value from a fluid spacing scale, if you are going down the [fluid typography](https://utopia.fyi) route (recommended, but your milage may vary). See [Utopia.fyi](https://utopia.fyi) for more details and a fluid type tool.

### 5. OpenType utility classes

```
.dlig { font-variant-ligatures: discretionary-ligatures; }
.hlig { font-variant-ligatures: historical-ligatures; }
.dlig.hlig { font-variant-ligatures: discretionary-ligatures historical-ligatures; } /* Apply both historic and discretionary */

.pnum {	font-variant-numeric: proportional-nums; }
.tnum { font-variant-numeric: tabular-nums;	}
.lnum {	font-variant-numeric: lining-nums; }
.onum {	font-variant-numeric: oldstyle-nums; }
.zero {	font-variant-numeric: slashed-zero;	}
.pnum.zero { font-variant-numeric: proportional-nums slashed-zero; } /* Apply slashed zeroes to proportional numerals */
.tnum.zero { font-variant-numeric: tabular-nums slashed-zero; }
.lnum.zero { font-variant-numeric: lining-nums slashed-zero; }
.onum.zero { font-variant-numeric: oldstyle-nums slashed-zero; }
.frac {	font-variant-numeric: diagonal-fractions; }
.afrc {	font-variant-numeric: stacked-fractions; }
.ordn {	font-variant-numeric: ordinal; }

.smcp {	font-variant-caps: small-caps; }
.c2sc { font-variant-caps: unicase; }
.hist {	font-variant-alternates: historical-forms; }
```

Helper utilities matching on/off Opentype layout features available through high level CSS properties.

```
@font-feature-values "Fancy Font Name" { /* match font-family webfont */
	@styleset { cursive: 1; swoopy: 7 16; }
	@character-variant { ampersand: 1; capital-q: 2; }
	@stylistic { two-story-g: 1; straight-y: 2; }
	@swash { swishy: 1; flowing: 2; wowzers: 3 }
	@ornaments { clover: 1; fleuron: 2; }
	@annotation { circled: 1; boxed: 2; }
}
```

Other Opentype features can have multiple glyphs, accessible via an index number defined in the font - these will be explained in documentation that came with your font. These vary between fonts, so you need to set up a new `@font-font-features` rule for each different font, ensuring the font name matches that of the font family. You then give each feature a custom name such as ‘swoopy’. Note that stylesets can be combined, which is why `swoopy` has a space-separated list of indices `7 16`.

```
/* Stylesets */
.ss01 {	font-variant-alternates: styleset(cursive); }
.ss02 {	font-variant-alternates: styleset(swoopy); }

/* Character variants */
.cv01 {	font-variant-alternates: character-variant(ampersand); }
.cv02 {	font-variant-alternates: character-variant(capital-q); }

/* Stylistic alternates */
.salt1 { font-variant-alternates: stylistic(two-story-g); }
.salt2 { font-variant-alternates: stylistic(straight-y); }

/* Swashes */
.swsh1 { font-variant-alternates: swash(swishy); }
.swsh2 { font-variant-alternates: swash(flowing); }

/* Ornaments */
.ornm1 { font-variant-alternates: ornaments(clover); }
.ornm2 { font-variant-alternates: ornaments(fleuron); }

/* Alternative numerals */
.nalt1 { font-variant-alternates: annotation(circled); }
.nalt2 { font-variant-alternates: annotation(boxed); }
```

Handy utility classes showing how to access the font feature values you set up earlier using the `font-variant-alternates` property. 

```
:root {
    --opentype-case: "case" off;
    --opentype-sinf: "sinf" off;
}

/* If class is applied, update custom property */
.case {
    --opentype-case: "case" on;
}

.sinf {
    --opentype-sinf: "sinf" on;
}

/* Apply current state of all custom properties, defaulting to off */
* { 
    font-feature-settings: var(--opentype-case, "case" off), var(--opentype-sinf, "sinf" off);
}
```

Set custom properties for OpenType features only available through low level `font-feature-settings`. We need this approach because `font-feature-settings` does not inherit in the same way as `font-variant`. See [Roel’s write-up, including how to apply the same methodology to custom variable font axes](https://pixelambacht.nl/2019/fixing-variable-font-inheritance/).

### 6. Generic helper classes

Some utilities to help ensure best typographic practice. 

```
.centered {
	text-align: center;
	text-wrap: balance;
}
```

When centring text you’ll almost always want the text to be ‘balanced’, meaning roughly the same number of characters on each line.

```
.uppercase {
	text-transform: uppercase;
	--opentype-case: "case" on;
}
```

When fully capitalising text, ensure punctuation designed to be used within caps is turned on where available, using the Opentype ‘case’ feature.

```
.smallcaps {
	font-variant-caps: all-small-caps;
	font-variant-numeric: oldstyle-nums;	
}
```

Transform both upper and lowercase letters to small caps, and use old style-numerals within runs of small caps so they match size-wise.

### 7. Prose styling defaults

Assign a `.prose` class to your running text, that is to say an entire piece of prose such as the full text of an article or blog post.

```
.prose {
	text-wrap: pretty;
	font-variant-numeric: oldstyle-nums proportional-nums;
	font-size-adjust: 0.507;
}
```

Firstly we get ourselves better widow/orphan control, aiming for blocks of text to not end with a line containing a word on its own. Also we use proportional old-style numerals in running text.

Also adjust the size of fallback fonts to match the webfont to maintain legibility with fallback fonts and reduce visible reflowing. The `font-size-adjust` number is the aspect ratio of the webfont, which you can [calculate using this tool](https://clagnut.com/sandbox/font-size-adjust-ex.html) (Chrome only).

```
strong, b, th { 
	font-weight: bold;
	font-size-adjust: 0.514; 
}
```

Apply a different adjustment to elements which are typically emboldened by default, as bold weights often have a different aspect ratio - check for the different weights you may be using, including numeric semi-bolds (eg. 650).  Headings are dealt with separately as the aspect ratio may be affected by optical sizing.

### 8. Headings

```
h1, h2, h3, h4 { 
	line-height: 1.1; 
	font-size-adjust: 0.514;
	font-variant-numeric: lining-nums; }
```

Set shorter line heights on your main headings. Set an aspect ratio for fallback fonts - check for different weights of headings. Use lining numerals in headings, especially when using Title Case.

```
h1 {
	font-variant-ligatures: discretionary-ligatures; 
	font-size-adjust: 0.521;
}
```

Turn on fancy ligatures for main headings. If the font has an optical sizing axis, you might need to adjust the aspect ratio accordingly.

```
h1.uppercase {
	font-variant-caps: titling-caps;
}
```

When setting a heading in all caps, use titling capitals which are specially designed for setting caps at larger sizes.

### 9. Sup and sub

Use proper super- and subscript characters. Apply to `SUB` and `SUP` elements as well as utility classes for when semantic sub/superscripts are not required.

```
@supports ( font-variant-position: sub ) {
	sub, .sub {
		vertical-align: baseline;
		font-size: 100%;
		line-height: inherit;
		font-variant-position: sub;
	}
}

@supports ( font-variant-position: super ) {
	sup, .sup {
		vertical-align: baseline;
		font-size: 100%;
		line-height: inherit;
		font-variant-position: super;
	}
}
```

If font-variant-position is not specified, browsers will synthesise sub/superscripts, so we need to manually turn off the synthesis. This is the only way to use a font's proper sub/sup glyphs, however it's **only safe to use this if** you know your font has glyphs for all the characters you are sub/superscripting. If the font lacks those characters (most only have sub/superscript numbers, not letters), then only Firefox (correctly) synthesizes sup and sub - all other browsers will display normal characters in the regular way as we turned the synthesis off.

```
.chemical { 
	--opentype-sinf: "sinf" on;
}
```

For chemical formulae like H2O, use scientific inferiors instead of `SUB`.

### 10. Tables, times and maths

```
td, math, time[datetime*=":"] {
	font-variant-numeric: tabular-nums lining-nums slashed-zero;	
}
```

Make sure all numbers in tables are lining tabular numerals, adding slashed zeroes for clarity. This could usefully apply where a time is specifically marked up, as well as in mathematics.

### 11. Quotes
Use curly quotes and hang punctuation around blockquotes.

```
:lang(en) > * {	quotes: '“' '”' '‘' '’' ; } /* “Generic English ‘style’” */
:lang(en-GB) > * {	quotes: '‘' '’' '“' '”'; } /* ‘British “style”’ */
:lang(fr) > * { quotes: "« " " »" "‹ " " ›"; } /* « French ‹ style › » */
```

Set punctuation order for inline quotes. Quotes are language-specific, so set a `lang` attribute on your `HTML` element or send the language via a server header.

```
q::before { content: open-quote }
q::after  { content: close-quote }
```

Insert quotes before and after `Q` element content.

```
.quoted, .quoted q {
    quotes: '“' '”' '‘' '’';
}
```

Punctuation order for blockquotes, using a utility class to surround with double-quotes.

```
.quoted p:first-of-type::before {
    content: open-quote;
}
.quoted p:last-of-type::after  {
    content: close-quote;
}
```

Append quotes to the first and last paragraphs in the blockquote.

```
.quoted p:first-of-type::before {
    margin-inline-start: -0.87ch; /* Adjust according to font */
}
.quoted p {
    hanging-punctuation: first last;
}
@supports(hanging-punctuation: first last) {
    .quoted p:first-of-type::before {
        margin-inline-start: 0;
    }
}
```

Hang the punctuation outside of the blockquote. Firstly manually hang punctuation with a negative margin,  then remove the manual intervention and use `hanging-punctuation` if supported.

### 12. Hyphenation
Turn on hyphenation for prose. Language is required in order for the browser to use the correct hyphenation dictionary.

```
.prose {
	-webkit-hyphens: auto;
	-webkit-hyphenate-limit-before: 4;
	-webkit-hyphenate-limit-after: 3;
	-webkit-hyphenate-limit-lines: 2;
	
	hyphens: auto;
	hyphenate-limit-chars: 7 4 3;
	hyphenate-limit-lines: 2;	
	hyphenate-limit-zone: 8%;
	hyphenate-limit-last: always;
}
```

Include additional refinements to hyphenation. Respectively, these stop short words being hyphenated, prevent ladders of hyphens, and reduce overall hyphenation a bit. Safari uses legacy properties to achieve some of the same effects, hence the ugly prefixes and slightly different syntax.

```
.prose pre, .prose code, .prose var, .prose samp, .prose kbd,
.prose h1, .prose h2, .prose h3, .prose h4, .prose h5, .prose h6 {
	-webkit-hyphens: manual;
	hyphens: manual;
}
```

Turn hyphens off for monospace and headings.

### 13. Dark mode
Reduce grade if available to prevent bloom of inverted type.

```
:root {
  --vf-grad: 0;
}

@media (prefers-color-scheme: dark) {
  :root {
    --vf-grad: -50;
  }
}

* {
  font-variation-settings: "GRAD" var(--vf-grad, 0);
}
```

Not all fonts have a grade (`GRAD`) axis, and the grade number is font-specific. We're using the customer property method because `font-variation-settings` provides low-level control meaning each subsequent use of the property completely overrides prior use - the values are not inherited or combined, unlike with `font-variant` for example.
