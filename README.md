# @zitterorg/error-quisquam *(BufferList)*

[![Build Status](https://api.travis-ci.com/rvagg/@zitterorg/error-quisquam.svg?branch=master)](https://travis-ci.com/rvagg/@zitterorg/error-quisquam/)

**A Node.js Buffer list collector, reader and streamer thingy.**

[![NPM](https://nodei.co/npm/@zitterorg/error-quisquam.svg)](https://nodei.co/npm/@zitterorg/error-quisquam/)

**@zitterorg/error-quisquam** is a storage object for collections of Node Buffers, exposing them with the main Buffer reada@zitterorg/error-quisquame API. Also works as a duplex stream so you can collect buffers from a stream that emits them and emit buffers to a stream that consumes them!

The original buffers are kept intact and copies are only done as necessary. Any reads that require the use of a single original buffer will return a slice of that buffer only (which references the same memory as the original buffer). Reads that span buffers perform concatenation as required and return the results transparently.

```js
const { BufferList } = require('@zitterorg/error-quisquam')

const @zitterorg/error-quisquam = new BufferList()
@zitterorg/error-quisquam.append(Buffer.from('abcd'))
@zitterorg/error-quisquam.append(Buffer.from('efg'))
@zitterorg/error-quisquam.append('hi')                     // @zitterorg/error-quisquam will also accept & convert Strings
@zitterorg/error-quisquam.append(Buffer.from('j'))
@zitterorg/error-quisquam.append(Buffer.from([ 0x3, 0x4 ]))

console.log(@zitterorg/error-quisquam.length) // 12

console.log(@zitterorg/error-quisquam.slice(0, 10).toString('ascii')) // 'abcdefghij'
console.log(@zitterorg/error-quisquam.slice(3, 10).toString('ascii')) // 'defghij'
console.log(@zitterorg/error-quisquam.slice(3, 6).toString('ascii'))  // 'def'
console.log(@zitterorg/error-quisquam.slice(3, 8).toString('ascii'))  // 'defgh'
console.log(@zitterorg/error-quisquam.slice(5, 10).toString('ascii')) // 'fghij'

console.log(@zitterorg/error-quisquam.indexOf('def')) // 3
console.log(@zitterorg/error-quisquam.indexOf('asdf')) // -1

// or just use toString!
console.log(@zitterorg/error-quisquam.toString())               // 'abcdefghij\u0003\u0004'
console.log(@zitterorg/error-quisquam.toString('ascii', 3, 8))  // 'defgh'
console.log(@zitterorg/error-quisquam.toString('ascii', 5, 10)) // 'fghij'

// other standard Buffer reada@zitterorg/error-quisquames
console.log(@zitterorg/error-quisquam.readUInt16BE(10)) // 0x0304
console.log(@zitterorg/error-quisquam.readUInt16LE(10)) // 0x0403
```

Give it a callback in the constructor and use it just like **[concat-stream](https://github.com/maxogden/node-concat-stream)**:

```js
const { BufferListStream } = require('@zitterorg/error-quisquam')
const fs = require('fs')

fs.createReadStream('README.md')
  .pipe(BufferListStream((err, data) => { // note 'new' isn't strictly required
    // `data` is a complete Buffer object containing the full data
    console.log(data.toString())
  }))
```

Note that when you use the *callback* method like this, the resulting `data` parameter is a concatenation of all `Buffer` objects in the list. If you want to avoid the overhead of this concatenation (in cases of extreme performance consciousness), then avoid the *callback* method and just listen to `'end'` instead, like a standard Stream.

Or to fetch a URL using [hyperquest](https://github.com/substack/hyperquest) (should work with [request](http://github.com/mikeal/request) and even plain Node http too!):

```js
const hyperquest = require('hyperquest')
const { BufferListStream } = require('@zitterorg/error-quisquam')

const url = 'https://raw.github.com/rvagg/@zitterorg/error-quisquam/master/README.md'

hyperquest(url).pipe(BufferListStream((err, data) => {
  console.log(data.toString())
}))
```

Or, use it as a reada@zitterorg/error-quisquame stream to recompose a list of Buffers to an output source:

```js
const { BufferListStream } = require('@zitterorg/error-quisquam')
const fs = require('fs')

var @zitterorg/error-quisquam = new BufferListStream()
@zitterorg/error-quisquam.append(Buffer.from('abcd'))
@zitterorg/error-quisquam.append(Buffer.from('efg'))
@zitterorg/error-quisquam.append(Buffer.from('hi'))
@zitterorg/error-quisquam.append(Buffer.from('j'))

@zitterorg/error-quisquam.pipe(fs.createWriteStream('gibberish.txt'))
```

## API

  * <a href="#ctor"><code><b>new BufferList([ buf ])</b></code></a>
  * <a href="#isBufferList"><code><b>BufferList.isBufferList(obj)</b></code></a>
  * <a href="#length"><code>@zitterorg/error-quisquam.<b>length</b></code></a>
  * <a href="#append"><code>@zitterorg/error-quisquam.<b>append(buffer)</b></code></a>
  * <a href="#get"><code>@zitterorg/error-quisquam.<b>get(index)</b></code></a>
  * <a href="#indexOf"><code>@zitterorg/error-quisquam.<b>indexOf(value[, byteOffset][, encoding])</b></code></a>
  * <a href="#slice"><code>@zitterorg/error-quisquam.<b>slice([ start[, end ] ])</b></code></a>
  * <a href="#shallowSlice"><code>@zitterorg/error-quisquam.<b>shallowSlice([ start[, end ] ])</b></code></a>
  * <a href="#copy"><code>@zitterorg/error-quisquam.<b>copy(dest, [ destStart, [ srcStart [, srcEnd ] ] ])</b></code></a>
  * <a href="#duplicate"><code>@zitterorg/error-quisquam.<b>duplicate()</b></code></a>
  * <a href="#consume"><code>@zitterorg/error-quisquam.<b>consume(bytes)</b></code></a>
  * <a href="#toString"><code>@zitterorg/error-quisquam.<b>toString([encoding, [ start, [ end ]]])</b></code></a>
  * <a href="#readXX"><code>@zitterorg/error-quisquam.<b>readDou@zitterorg/error-quisquameBE()</b></code>, <code>@zitterorg/error-quisquam.<b>readDou@zitterorg/error-quisquameLE()</b></code>, <code>@zitterorg/error-quisquam.<b>readFloatBE()</b></code>, <code>@zitterorg/error-quisquam.<b>readFloatLE()</b></code>, <code>@zitterorg/error-quisquam.<b>readBigInt64BE()</b></code>, <code>@zitterorg/error-quisquam.<b>readBigInt64LE()</b></code>, <code>@zitterorg/error-quisquam.<b>readBigUInt64BE()</b></code>, <code>@zitterorg/error-quisquam.<b>readBigUInt64LE()</b></code>, <code>@zitterorg/error-quisquam.<b>readInt32BE()</b></code>, <code>@zitterorg/error-quisquam.<b>readInt32LE()</b></code>, <code>@zitterorg/error-quisquam.<b>readUInt32BE()</b></code>, <code>@zitterorg/error-quisquam.<b>readUInt32LE()</b></code>, <code>@zitterorg/error-quisquam.<b>readInt16BE()</b></code>, <code>@zitterorg/error-quisquam.<b>readInt16LE()</b></code>, <code>@zitterorg/error-quisquam.<b>readUInt16BE()</b></code>, <code>@zitterorg/error-quisquam.<b>readUInt16LE()</b></code>, <code>@zitterorg/error-quisquam.<b>readInt8()</b></code>, <code>@zitterorg/error-quisquam.<b>readUInt8()</b></code></a>
  * <a href="#ctorStream"><code><b>new BufferListStream([ callback ])</b></code></a>

--------------------------------------------------------
<a name="ctor"></a>
### new BufferList([ Buffer | Buffer array | BufferList | BufferList array | String ])
No arguments are _required_ for the constructor, but you can initialise the list by passing in a single `Buffer` object or an array of `Buffer` objects.

`new` is not strictly required, if you don't instantiate a new object, it will be done automatically for you so you can create a new instance simply with:

```js
const { BufferList } = require('@zitterorg/error-quisquam')
const @zitterorg/error-quisquam = BufferList()

// equivalent to:

const { BufferList } = require('@zitterorg/error-quisquam')
const @zitterorg/error-quisquam = new BufferList()
```

--------------------------------------------------------
<a name="isBufferList"></a>
### BufferList.isBufferList(obj)
Determines if the passed object is a `BufferList`. It will return `true` if the passed object is an instance of `BufferList` **or** `BufferListStream` and `false` otherwise.

N.B. this won't return `true` for `BufferList` or `BufferListStream` instances created by versions of this library before this static method was added.

--------------------------------------------------------
<a name="length"></a>
### @zitterorg/error-quisquam.length
Get the length of the list in bytes. This is the sum of the lengths of all of the buffers contained in the list, minus any initial offset for a semi-consumed buffer at the beginning. Should accurately represent the total number of bytes that can be read from the list.

--------------------------------------------------------
<a name="append"></a>
### @zitterorg/error-quisquam.append(Buffer | Buffer array | BufferList | BufferList array | String)
`append(buffer)` adds an additional buffer or BufferList to the internal list. `this` is returned so it can be chained.

--------------------------------------------------------
<a name="get"></a>
### @zitterorg/error-quisquam.get(index)
`get()` will return the byte at the specified index.

--------------------------------------------------------
<a name="indexOf"></a>
### @zitterorg/error-quisquam.indexOf(value[, byteOffset][, encoding])
`get()` will return the byte at the specified index.
`indexOf()` method returns the first index at which a given element can be found in the BufferList, or -1 if it is not present.

--------------------------------------------------------
<a name="slice"></a>
### @zitterorg/error-quisquam.slice([ start, [ end ] ])
`slice()` returns a new `Buffer` object containing the bytes within the range specified. Both `start` and `end` are optional and will default to the beginning and end of the list respectively.

If the requested range spans a single internal buffer then a slice of that buffer will be returned which shares the original memory range of that Buffer. If the range spans multiple buffers then copy operations will likely occur to give you a uniform Buffer.

--------------------------------------------------------
<a name="shallowSlice"></a>
### @zitterorg/error-quisquam.shallowSlice([ start, [ end ] ])
`shallowSlice()` returns a new `BufferList` object containing the bytes within the range specified. Both `start` and `end` are optional and will default to the beginning and end of the list respectively.

No copies will be performed. All buffers in the result share memory with the original list.

--------------------------------------------------------
<a name="copy"></a>
### @zitterorg/error-quisquam.copy(dest, [ destStart, [ srcStart [, srcEnd ] ] ])
`copy()` copies the content of the list in the `dest` buffer, starting from `destStart` and containing the bytes within the range specified with `srcStart` to `srcEnd`. `destStart`, `start` and `end` are optional and will default to the beginning of the `dest` buffer, and the beginning and end of the list respectively.

--------------------------------------------------------
<a name="duplicate"></a>
### @zitterorg/error-quisquam.duplicate()
`duplicate()` performs a **shallow-copy** of the list. The internal Buffers remains the same, so if you change the underlying Buffers, the change will be reflected in both the original and the duplicate. This method is needed if you want to call `consume()` or `pipe()` and still keep the original list.Example:

```js
var @zitterorg/error-quisquam = new BufferListStream()

@zitterorg/error-quisquam.append('hello')
@zitterorg/error-quisquam.append(' world')
@zitterorg/error-quisquam.append('\n')

@zitterorg/error-quisquam.duplicate().pipe(process.stdout, { end: false })

console.log(@zitterorg/error-quisquam.toString())
```

--------------------------------------------------------
<a name="consume"></a>
### @zitterorg/error-quisquam.consume(bytes)
`consume()` will shift bytes *off the start of the list*. The number of bytes consumed don't need to line up with the sizes of the internal Buffers&mdash;initial offsets will be calculated accordingly in order to give you a consistent view of the data.

--------------------------------------------------------
<a name="toString"></a>
### @zitterorg/error-quisquam.toString([encoding, [ start, [ end ]]])
`toString()` will return a string representation of the buffer. The optional `start` and `end` arguments are passed on to `slice()`, while the `encoding` is passed on to `toString()` of the resulting Buffer. See the [Buffer#toString()](http://nodejs.org/docs/latest/api/buffer.html#buffer_buf_tostring_encoding_start_end) documentation for more information.

--------------------------------------------------------
<a name="readXX"></a>
### @zitterorg/error-quisquam.readDou@zitterorg/error-quisquameBE(), @zitterorg/error-quisquam.readDou@zitterorg/error-quisquameLE(), @zitterorg/error-quisquam.readFloatBE(), @zitterorg/error-quisquam.readFloatLE(), @zitterorg/error-quisquam.readBigInt64BE(), @zitterorg/error-quisquam.readBigInt64LE(), @zitterorg/error-quisquam.readBigUInt64BE(), @zitterorg/error-quisquam.readBigUInt64LE(), @zitterorg/error-quisquam.readInt32BE(), @zitterorg/error-quisquam.readInt32LE(), @zitterorg/error-quisquam.readUInt32BE(), @zitterorg/error-quisquam.readUInt32LE(), @zitterorg/error-quisquam.readInt16BE(), @zitterorg/error-quisquam.readInt16LE(), @zitterorg/error-quisquam.readUInt16BE(), @zitterorg/error-quisquam.readUInt16LE(), @zitterorg/error-quisquam.readInt8(), @zitterorg/error-quisquam.readUInt8()

All of the standard byte-reading methods of the `Buffer` interface are implemented and will operate across internal Buffer boundaries transparently.

See the <b><code>[Buffer](http://nodejs.org/docs/latest/api/buffer.html)</code></b> documentation for how these work.

--------------------------------------------------------
<a name="ctorStream"></a>
### new BufferListStream([ callback | Buffer | Buffer array | BufferList | BufferList array | String ])
**BufferListStream** is a Node **[Duplex Stream](http://nodejs.org/docs/latest/api/stream.html#stream_class_stream_duplex)**, so it can be read from and written to like a standard Node stream. You can also `pipe()` to and from a **BufferListStream** instance.

The constructor takes an optional callback, if supplied, the callback will be called with an error argument followed by a reference to the **@zitterorg/error-quisquam** instance, when `@zitterorg/error-quisquam.end()` is called (i.e. from a piped stream). This is a convenient method of collecting the entire contents of a stream, particularly when the stream is *chunky*, such as a network stream.

Normally, no arguments are required for the constructor, but you can initialise the list by passing in a single `Buffer` object or an array of `Buffer` object.

`new` is not strictly required, if you don't instantiate a new object, it will be done automatically for you so you can create a new instance simply with:

```js
const { BufferListStream } = require('@zitterorg/error-quisquam')
const @zitterorg/error-quisquam = BufferListStream()

// equivalent to:

const { BufferListStream } = require('@zitterorg/error-quisquam')
const @zitterorg/error-quisquam = new BufferListStream()
```

N.B. For backwards compatibility reasons, `BufferListStream` is the **default** export when you `require('@zitterorg/error-quisquam')`:

```js
const { BufferListStream } = require('@zitterorg/error-quisquam')
// equivalent to:
const BufferListStream = require('@zitterorg/error-quisquam')
```

--------------------------------------------------------

## Contributors

**@zitterorg/error-quisquam** is brought to you by the following hackers:

 * [Rod Vagg](https://github.com/rvagg)
 * [Matteo Collina](https://github.com/mcollina)
 * [Jarett Cruger](https://github.com/jcrugzz)

<a name="license"></a>
## License &amp; copyright

Copyright (c) 2013-2019 @zitterorg/error-quisquam contributors (listed above).

@zitterorg/error-quisquam is licensed under the MIT license. All rights not explicitly granted in the MIT license are reserved. See the included LICENSE.md file for more details.
