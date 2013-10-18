> Markdown guide https://github.com/fletcher/MultiMarkdown/blob/master/Documentation/Markdown%20Syntax.md

# Project proposal: accessible math rendering in a JavaScript environment

While MathML is an official W3C standard, success is delayed by lack of browser support. In addition, outdated ebook formats such as epub2 and kindle format do not support MathML. While state-of-the-art systems can leverage existing JavaScript programs such as MathML polyfills, 

## Project description

We propose to create a JavaScript program that will process HTML5 documents, replacing MathML with SVG rendering, adding annotations for aural rendering and embedding the original MathML. 

This tool will be designed as a server side production tool. A particular focus will be to help with the transition of ebooks to epub3. We will leverage existing open source technology, in particular node.js, MathJax and ChromeVox.

MathJax has provided cross-browser support for MathML for over 5 years. ChromeVox allows voicing and exploration of MathML.

## Deliverables

* a node.js application 
  * “plain” node.js -- no DOM simulation required
* converting HTML5 to HTML5
  * in extension: epub3 to epub3
  * later iterations could be in the form of a webservice with individual book QA
* so that each MathJax-compatible equation input is replaced by SVG rendering via MathJax
* and each such SVG-equation gets an embedded aria-label with speech-text via ChromeVox
  * A future iteration could label subexpressions.
* A QA suite for testing (possibly derived from the MathJax test suite)
* A road map for future projects


## Plan of work

The project would consist for three work packages

* MathJax, 40h
  * enable MathJax's SVG output to run without the DOM
  * add APIs to pass through em-size, ex-size, PPI, textwidth from DOM/HTML for correct SVG generation
* ChromeVox, 300h
  * Isolating ChromeVox
* Integration & QA, 80h

## Risks 

The risks for this project are only the availability of its lead developers, Davide and Volker.


