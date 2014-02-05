# Project proposal: accessible math rendering in a JavaScript environment

## Background

Mathematical formulas on the web can be represented in their own specialized
markup language, [MathML](http://www.w3.org/TR/MathML3). But although MathML is
an official W3C standard in the [HTML5](http://www.w3.org/TR/html5)
specification, thus by extension also part of the
[ePub3](http://idpf.org/epub/30) standard, not all major browsers also implement
MathML rendering. In addition, outdated ebook formats such as epub2 and kindle
format do not support MathML at all. While state-of-the-art systems can close
the gap to full compliance with the above standards using HTML5 cross browser
polyfills, which are generally JavaScript programs that emulate functionality
not natively implemented in a client browser. They have to be made explicitly
available, either by a user installing them locally or by the web content
provider by including a call to the online resource in their site.
This has some clear drawbacks in that

1. polyfill solutions are generally less performant than browser functionality,

1. web content providers have to rely on appropriate polyfills being available
   client site,

1. content can only be made correctly available when viewd online, which is not
   useful for offline solutions such as eBook readers.

In the case of MathML the polyfill solution is 
[MathJax](http://www.mathjax.org),
a JavaScript display engine that consistently renders mathematical expressions
in all browsers. It handles a number of input formats and translates them into
simple HTML markup that visually renders a math expression in a browser,
regardless of whether it supports MathML. It is generally included by web
authors as JavaScript call to MathJax's content distribution network to enable
client side rendering of math expressions.

Consequently this approach suffers from the problems enumerated above. In
particular, MathJax rendering is generally only available online and, due to the
complexity involved in setting mathematical expressions and the potentially
large number of fonts needed, the performance of page rendering can be
significantly affected especially for handheld devices such as tablets. These
proplems are often compounded as content essentially has to be re-rendered every
time a page is loaded.

In addition to the general issues of displaying mathematics, there is the
problem providing accessibility to mathematical content for readers with special
needs such as visual impairments or learning disabilities.  While some solutions
to make mathematical content given MathML or even MathJax exist, they are either
specialist systems, plugins for particular browsers or screenreaders that are
bound to specific platforms or browsers. Moreover, there is little support for
speaking mathematical content in current eBook readers. This is particularly
problematic given the fact the fact that more and more modern teaching material
will become available exclusively in online format or as eBooks.

As a consequence one can identify a clear need to provide a short to medium term
solution for making accessible mathematical content available in a platform
independent way. In our scenario this means to have a way to include fully
rendered mathematics directly into HTML5 content, that can be served to a
diverse set of platforms and form factors. Hence it has to be both scalable and
allow for dynamic reflow, which rules out simple rendering as images of a static
size. Moreover, it has to contain a verbal description of the formula that can
be picked up and aurally rendered by any screen reader.

## Project Objective

We propose a software system for rendering mathematical expressions in an
annotated SVG format that will allow for browser independent, fully scalable
display of formulas on the web and their accessibility by any general screen
reader via aural rendering of the annotations.  Our program will be written in
Javascript, building on already existing technologies: The MathJax display
engine for browser independent rendering of mathematics and the speech rule
engine of the ChromeVox screen reading system.

## Consumers, Users, Beneficiaries

* Content producers will be able to render MathML statically while retaining 
accessibility
* Users will find acccessible rendering of mathematical content without loosing 
the ability to leverage MathML
* In particular, we will collaborate with the Wikimedia Foundation so that the
Media Wiki Math extension can use the proposed tool


## Project description

We propose to create a JavaScript program that will process HTML5 documents,
replacing MathML with SVG rendering, adding both annotations for aural rendering
as well as embedding the original MathML expression. This tool will be designed
as a server side production tool to enable the production of documents whose
mathematical formulas can be displayed efficiently both online and offline
without the need for client-side rendering and that are fully accessible
independent of the reader's platform via the provision of audio annotations.

Consequently, one particular focus of the project will be to help with the
transition of ebooks to accessible epub3.  For our work we will leverage
existing open source technology, in particular

* NodeJS, a JavaScript environment that allows to run web applications outside a
  particular browser.
* MathJax, a JavaScript display engine that renders mathematical expressions in
  all browsers. MathJax has provided cross-browser support for MathML for over
  5 years.
* ChromeVox, a screen reader specialising on web documents that is available as
  a Chrome extension and written in JavaScript.  ChromeVox particularly allows
  voicing and exploration of MathML.


### 

[MathJax](http://www.mathjax.org) is a JavaScript display engine that process
mathematical expressions given in one of three different formats --- LaTeX,
MathML, or AsciiMath --- and translates them into markup that can be visually
rendered in all browsers, regardless of their native support for math rendering.
In this project we want to exploit MathJax's ability to render mathematical
formulae into scalable vector graphics (SVG), an XML representation that can be
structured by grouping together graphical elements into meaningful
components. The SVG structure is then annotated by ARIA labels containing audio
descriptions of the formula that is represented.  These annotations will be
computed using the speech rule engine of the ChromeVox screen reader.

[ChromeVox](http://www.chromevox.com) is a screen reader that has been built
with the aim of making rich web applications accessible to visually impaired
users. ChromeVox runs inside the Chrome browser where it can be installed as an
extension and it is the default screen reader for Chrome OS. Therefore, it uses
exclusively the HTML document object model (DOM) to communicate with web
applications without the need to use an accessibility API provided by the
underlying operating system. ChromeVox has currently support for screen reading
of mathematical expression given either directly as MathML or rendered by
MathJax. The aural rendering of expressions is achieved by a speech rule engine
that can be customized flexibly along several axes. And while the engine is
capable of rendering general XML exrepessions as long as appropriate speech
rules are available, it is embedded into ChromeVox screen reading architecture
and adapted to the overall structure of a Chrome extension.

The goal of our project is to combine both MathJax's visual SVG rendering with
the aural rendering of ChromeVox's speech rule engine in a standalone system
that can be run server side in NodeJS, a JavaScript implementation that is
independent of any browser. The main technical challenge of the project is
therefore to detach both MathJax and the speech rule engine from the DOM and in
the case of the latter also to ensure that it functions independent of
ChromeVox's overall architecture. In more detail we envision the following 

Currently MathJax works as a client side Javascript library that can be injected
into page content and that exploits information from a page's DOM as well as
from a browsers render tree to compute SVG structures. 

* The main tasks to make it a server side application are therefore to isolate
  MathJax's SVG output to run without a DOM.  Currently, the SVG output processor
  converts the internal MathML representation into DOM elements within the page.
  One approach would be to create a document fragment into which the SVG output
  would be added, and then serialize that after it is complete.  This would
  require only small changes to the core SVG output jax.  Alternatively, since
  all element creation is done through a small set of functions, these could be
  replaced by ones that produce a serialization of the output directly.

* An important aspect of computing SVG representations is to gauge appropriate
  sizes for the rendered page and text where the mathematical formula eventually
  will be placed.  The SVG renderer needs to know the em-size and ex-size of the
  surrounding font (in terms of pixels), and the width of the space available
  for the mathematics.  The latter is needed to allow line-breaking to occur
  properly, and to position equation numbers in the proper locations.  This
  information will need to be provided to the SVG renderer since it normally
  obtains these from the DOM.

Contrary to MathJax, ChromeVox's speech rule engine depends less on information
from DOM but can also work with alternative XML representation. Thus, decoupling
its functionality from the DOM will be a smaller effort.  On the other hand, the
engine itself is not a system in its own right yet, but part of the overall
ChromeVox system. Consequently, more effort will have to be expended to
transform it into a standalone module.

As ChromeVox is implemented as a Chrome extension its architecture is affected
by some of the peculiarities of how these extensions work. In a nutshell a
Chrome extension works by injecting instances of itself into the content script
of every single DOM in Chrome; that is, in practice one instance runs in every
tab open. Single instances of an extension are coordinated via a single
background page and communication between the different parts is achieved via
message parsing, only. ChromeVox itself uses this architecture for interacting
with the DOM in content scripts while collating text in the background page to
send to the text to speech engine.

The speech rule engine is also implemented in a way that makes full use of the
advantages a Chrome extension architecture provides, in that parts of the
translation into speech strings is performed in the content script -- in
particular the recursive descend into structures in the DOM -- while others --
for example, the mapping of Unicode characters -- are done in the background
page.

Thus we can identify the three specific tasks to make the rule engine
independent:

* Make translation of HTML elements independent of the DOM.

* Disentangle the rule engine from peripheral ChromeVox functionality it uses.

* Combine the seperate engine parts from content and background scipts into one
  monolithic system.

However, when doing these technical changes we want to avoid branching off the
development of the speech rule engine in our project of the development trunk of
ChromeVox. In other words, we want to ensure that both the ChromeVox project and
our project will mutually benefit from further developments to the engine in
either project. As a consequence we intend to spend some work on making sure
that our technical changes are ring fenced, in the sense that we can still share
the bulk of the code with ChromeVox proper and that we can wire our version of
the engine into ChromeVox's build system.  ChromeVox already has adequate
facilities that allow adaptation of the systems to be made for different
platforms. We want to leverage those facilities in order to achieve the goal of
being able to build our standalone rule engine directly from the ChromeVox
trunk.


As a final adaptation of the rule engine we will construct a dedicated rule set
for the translation. Within ChromeVox the engine is equipped with two sets of
speech rules: one for a faithful structural description of the formula and one
aiming at reading formulas in a more human oriented style, which based on a
special semantic representation. Unfortunately, we can not directly import those
sets into our project, as they both make heavily use of ChromeVox's features to
influence the behaviour of the text to speech engine directly, by conveying
meaning of a formula via changes in intonation and pauses. For example, changes
in sub- or superscript level are modelled by variations of the pitch, pauses
indicate bracketing or compound structures, etc.  This is obviously of not
useful for our project at this point, where we can only integrate text strings,
but simply omitting the text to speech instructions will lead to ambiguous
reading of formulae. Hence we will implement a bespoke rule set for our project
based on the semantic interpretations and modelled more closely after the
[MathSpeak](http://www.gh-mathspeak.com/examples/grammar-rules/) rules.


Finally, once both MathJax and Speech Rule Engine are have been transformed into
standalone system, we will define an appropriate interface between the two and
integrate them into a single workflow to transform mathematical expressions into
annotated SVG images. To validate the effectiveness of the system and assure the
quality of the results we will design a test suite, initially derived from the
MathJax test suite. The final combination will be able to process HTML5 
documents and its design should allow an extension to process epub files.

Furthermore, at the end of the project plan to deliver a road map for future
extension of the system. This future work could involve

1. exploiting the possiblities of structuring SVG expressions by grouping
elements in order to label subexpressions,

2. supporting upcoming SVG accessibility features, 

3. or including additional instructions for the HTML5 speech API.

**Should we already mention those?**


### 


## Timeline and Budget

> Provide an estimated project timeline and budget

### Plan of work

The project is comprised of three work packages, which are in turn broken down
into single tasks.

1. MathJax, 40h
  1. isolate MathJax's SVG output to run without a DOM
  * add APIs to pass through em-size, ex-size, PPI, textwidth from DOM/HTML for 
correct SVG generation
* Speech Rule Engine, 300h
  1. Decouple the rule engine ChromeVox output to run without a DOM (37.5h)
  * Combine engine into a monolithic system (75h)
  * Extract the rule engine from peripheral ChromeVox functionality (37.5h)
  * Rewire the ChromeVox build process to allow for speech rule engine and this
  project. (75h)
  * Implement the full set of MathSpeak rules (75h)
* Integration & QA, 80h

## Deliverables

* a node.js application 
  * “plain” node.js -- no DOM simulation required
* converting HTML5 to HTML5
  * in later iterations: epub3 to epub3
  * in later iterations: a webservice with individual book QA
* so that each MathJax-compatible equation input  (MathML, TeX, Asciimath) is 
replaced by SVG rendering via MathJax
* and each such SVG-equation gets an embedded aria-label with speech-text via 
ChromeVox
  * In later iterations: label subexpressions.
  * in later iterations: move towards upcoming accessible SVG features
* A QA suite for testing (possibly derived from the MathJax test suite)
* A road map for future projects

## Resources

* Development
    * David Cervone
    * Volker Sorge
* Project management
    * Peter Krautzberger

We do not expect costs beyond personnel costs.

## Acceptance Criteria

> Only applies to software projects. Usually boilerplate statement about mutually agreed upon acceptance criteria. 

## Constraints and Assumptions

The project will be licensed under Apache License v2, cf. 
https://www.apache.org/licenses/

The development will be organized on github or similar public webservices.

## Risks 

The risks for this project are only the availability of its lead developers, 
Davide and Volker.
