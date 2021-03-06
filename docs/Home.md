# Libxmljs

LibXML bindings for Google's v8 javascript engine.

It also plays nicely with [[node.js|http://nodejs.org/]]

## Basics

* GitHub Repo - [[http://github.com/polotek/libxmljs]]
* Docs - [[http://github.com/polotek/libxmljs/wiki]]
* Mailing list - [[http://groups.google.com/group/libxmljs]]

## Requirements

* libxml
* node.js
* v8 (comes bundled with node, no need to install)
* [scons](http://www.scons.org/) (for building)

## Installation

To build the addon, the libxml C library must be installed and the xml2-config helper script must be on your path.

**npm**
    npm install libxmljs

**source**

In the root of the source directory, just run `make`.  This will generate libxmljs.node in the root of the source folder.  You can copy this file to `~/.node_libraries` or any other directory in your require path.

**node**
    var libxml = require('libxmljs');

## API

Some examples below or check out the full [[Documentation|#api]]

### Builder

There are two main ways to use the builder. The first:

    var doc = new libxml.Document(function(n) {
      n.node('root', function(n) {
        n.node('child', {foo: 'bar'}, function(n) {
          n.node('grandchild', {baz: 'fizbuzz'}, 'grandchild content');
        });
        n.node('sibling', 'with content!');
      });
    });

Calling doc.toString() will yield the following XML:

    <?xml version="1.0" encoding="UTF-8"?>
    <root>
        <child foo="bar">
            <grandchild baz="fizbuzz">grandchild content</grandchild>
        </child>
        <sibling>with content!</sibling>
    </root>

The other way is through chaining (indentation is meant to represent location in the tree):

    var doc = new libxml.Document();
      doc.node('root')
        .node('child', {foo: 'bar'})
          .node('grandchild', {baz: 'fizbuzz'}, 'grandchild content')
        .parent()
      .parent()
        .node('sibling', 'with content!');

This will yield the same results.

### Parsing

By file:

    var doc = libxml.parseXmlFile([filename]);

By String:

    var doc = libxml.parseXmlString([xmlString]);

#### SAX Parsing

Callbacks for SAX are created during the instantiation of the parser object. You can omit any callback you're not interested in. Below is just a list of callbacks currently supported with the arguments they'll be sent.

    var parser = new libxml.SaxParser(function(cb) {
      cb.onStartDocument(function() {});
      cb.onEndDocument(function() {});
      cb.onStartElementNS(function(elem, attrs, prefix, uri, namespaces) {});
      cb.onEndElementNS(function(elem, prefix, uri) {});
      cb.onCharacters(function(chars) {});
      cb.onCdata(function(cdata) {});
      cb.onComment(function(msg) {});
      cb.onWarning(function(msg) {});
      cb.onError(function(msg) {});
    });

    // Parse a string:
    parser.parseString([xmlString]);

    // Parse a file:
    parser.parseFile([filename]);

#### SAX Push Parsing

Push parsers are created the same way DOM parsers are, but take input a chunk at a time:

    var parser = new libxml.SaxPushParser(function(cb) {
      cb.onStartDocument(function() {});
      cb.onEndDocument(function() {});
      cb.onStartElementNS(function(elem, attrs, prefix, uri, namespaces) {});
      cb.onEndElementNS(function(elem, prefix, uri) {});
      cb.onCharacters(function(chars) {});
      cb.onCdata(function(cdata) {});
      cb.onComment(function(msg) {});
      cb.onWarning(function(msg) {});
      cb.onError(function(msg) {});
    });

    while(xmlChunk)
      parser.push(xmlChunk);

<a name="api"></a>
