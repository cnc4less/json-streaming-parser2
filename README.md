# json-streaming-parser
Arduino library for parsing potentially huge json streams on devices with scarce memory.

This library is a port of Salsify's PHP based json streaming parser (https://github.com/salsify/jsonstreamingparser).

## Why yet another JSON parser?

When working with small (connected) devices you might quickly get to the point where you need to process potentially huge JSON object received from a REST interface.
All libraries for processing JSON objects on the Arduino platform had some deficiencies which led me to create one on my own.
Not really being an expert in writing parsers I miserably failed the first time. 

This is the second attempt and I took Salsify's PHP based parser and ported it to C++. 
This library has been tested on the Arduino/ESP8266 target platform but should work in theory on all platforms available for the Arduino environment

## Why a streaming parser?

Generally speaking when parsing data you have two options to make sense of this data: 
you either create a document object model (DOM) which contains the whole information of that document and lets you retrieve the
nodes with random access. A DOM parser needs the whole document to start parsing and only lets you access the data
after it has finished the process.
The other option you have when parsing a document is to process it char by char (or byte by byte) while it becomes
available to your client code. Compared to the DOM parser this has two advantages: a) you can react
the data as soon as meaningful parts are available and b) you can drop information as soon as the parser has processed
it. This reduces the memory consumption a lot, especially if you retrieve huge documents when only being interested
by a small subset of it. But this efficiency comes at a price: your code will have to do more "magic" than with a
DOM parser, the business logic becomes part of the parser.

## How to use

This is a streaming parser, which means that you feed a stream of chars into the parser and you take out from that
stream whatever you are interested in. In order to do that you will create a subclass of JsonListener class and
implement methods which will be notified in case of certain events in the feed occure. Available events are:

 * startDocument()
 * key(String key)
 * value(String value)
 * endArray()
 * endObject()
 * endDocument()
 * startArray()
 * startObject()

In your implementation of these methods you will have to write problem specific code to find the proper