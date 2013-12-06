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
diverse set of platforms and form factors. Hence it has to both scalable and
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
replacing MathML with SVG rendering, adding annotations for aural rendering and
embedding the original MathML.

This tool will be designed as a server side production tool. A particular focus
will be to help with the transition of ebooks to epub3. We will leverage
existing open source technology, in particular node.js, MathJax and ChromeVox.

MathJax has provided cross-browser support for MathML for over 5
years. ChromeVox allows voicing and exploration of MathML.

[ChromeVox](http://www.chromevox.com) is a screen reader that has been
built with the aim of making rich web applications accessible to visually 
impaired
users. ChromeVox runs inside the Chrome browser where it can be installed as an
extension and it is the default screen reader for Chrome OS. Therefore, it uses
exclusively the HTML document object model (DOM) to communicate with web
applications without the need to use an accessibility API provided by the
underlying operating system.

## Timeline and Budget

> Provide an estimated project timeline and budget

### Plan of work

The project would consist for three work packages

* MathJax, 40h
  * isolate MathJax's SVG output to run without a DOM
  * add APIs to pass through em-size, ex-size, PPI, textwidth from DOM/HTML for 
correct SVG generation
* ChromeVox, 300h
  * Isolating ChromeVox output to run without a DOM
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
