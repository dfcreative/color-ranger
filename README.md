<img src="https://cdn.rawgit.com/dfcreative/color-ranger/design/logo.png" height="190"/>

<code>**C O L O R − R A N G E R**</code>&nbsp; renders a color range for a particular space in rectangular or polar coordinate system by manipulating _ImageData_. It is primarily needed for building color pickers.

* It can be used both in web-worker and in document thread.
* It is only [Nkb] minified & gzipped.
* You can require only needed renderers to reduce size even more.
* See [demo](jsfiddle), [range list and tests](https://cdn.rawgit.com/dfcreative/color-ranger/master/test/index.html).

<p>
	<a href="https://travis-ci.org/dfcreative/color-ranger"><img src="https://travis-ci.org/dfcreative/color-ranger.svg?branch=master"/></a>
	<a href="https://codeclimate.com/github/dfcreative/color-ranger"><img src="https://codeclimate.com/github/dfcreative/color-ranger/badges/gpa.svg"/></a>
	<a href="https://coveralls.io/r/dfcreative/color-ranger"><img src="https://img.shields.io/coveralls/dfcreative/color-ranger.svg"/></a>
	<a href="https://david-dm.org/dfcreative/color-ranger"><img src="https://david-dm.org/dfcreative/color-ranger.svg"/></a>
	<a href="http://unlicense.org/UNLICENSE"><img src="http://upload.wikimedia.org/wikipedia/commons/6/62/PD-icon.svg" width="20"/></a>
</p>



<!--
You may also be interesting in checking out picky - a color picker based on that.
-->


# Get started

### Install

The best way to use color ranger is to [browserify](https://github.com/substack/node-browserify) it as a requirement.

1. Install local package:
`$ npm install --save color-ranger`

2. Build bundle:
`browserify -r color-ranger > bundle.js`
Or if you have your own package, append color-ranger to it:
`browserify -r color-ranger -r your-dependency your-package.js > bundle.js`

3. Include the bundle: `<script src="bundle.js"></script>`
4. Finally require color-ranger module:
```html
<script>
	var ranger = require('color-ranger');
</script>
```


Alternately you can use a standalone version, comprising `color-space` module. Include [color-ranger.js](https://raw.githubusercontent.com/dfcreative/color-ranger/master/color-ranger.js) before you’ll use it:

```html
<script src="https://cdn.rawgit.com/dfcreative/color-ranger/master/color-ranger.js"></script>
```

You will get a `window.colorRanger` object, comprising rendering functions.


### Use

You need to setup a canvas first to make color-ranger work:

```html
<script>
	//create a canvas
	var canvas = document.createElement('canvas');
	canvas.width = 50;
	canvas.height = 50;
	var context = canvas.getContext('2d');
	var data = context.getImageData(0, 0, canvas.width, canvas.height);

	//render hue and saturation channels [0,1] for the rgb-color [127, 255, 255]
	//with min values [0,0] and max values [360, 100]
	data = colorRanger.renderRect([127,255,255], 'hsl', [0,1], [0,0], [360,100], data);

	//put image data back to canvas
	context.putImageData(data, 0, 0);

	//get a background with the rendered range
	document.body.style.backgroundImage = 'url(' + canvas.toDataURL() + ')';
</script>
```

You’ll see a range rendered as `body` background. You can see full list of ranges in [tests page](https://cdn.rawgit.com/dfcreative/color-space/master/test/index.html).


# API

[polar/rect image]

##### `.renderRect(rgb, space, channel, min, max, imageData)`
##### `.renderPolar(rgb, space, channel, min, max, imageData)`

Render rectangular or polar range into an `imageData`. Size of the final image is taken such that it fills the whole `imageData` area.

| Parameter | Type | Description |
|----|----|----|
| rgb | _Array_ | An array of rgb values, representing a color. E. g. `[0, 255, 127]`. |
| space | _string_ | A color space name for the range taken from the [color-space](https://github.com/dfcreative/color-space/) module. E. g. `'hsl'`. |
| channel | _Array_ | A tuple of x/y space channel indexes. E. g. `[0,2]` from `'hsv'` is hue and value. One of the channels can be omitted, e. g. `[null, 1]` means render saturation on y-axis. |
| min, max | _Array_ | Arrays of left and right values for the range, corresponding to the channels in x/y axis. |
| imageData | _ImageData_ | An `ImageData` object to which render a range. |

<br/>

[chess grid image]

##### `.renderGrid(rgbA, rgbB, imageData)`

Render a chess grid, useful for transparency grid image rendering. Grid size is automatically figured out from the `imageData` size.

| Parameter | Type | Description |
|----|----|----|
| rgbA | _Array_ | An rgb values for the "black cell" color. |
| rgbB | _Array_ | An rgb values for the right color. |
| imageData | _ImageData_ | An `ImageData` object to which render the grid bitmap. |


##### `.getWebworker(spaces)`

Return a web-worker able to render any space range for the passed set of spaces. `spaces` should be a `color-space` module or it’s custom build.

```js
var spaces = require('color-space');

//set up and run worker
var rangerWorker = ranger.getWorker(spaces);

//catch worker response
rangerWorker.addEvenListener('message', function(evt){
	if (evt.data.id !== 1) return;

	//image data is returned as `event.data.data`
	context.putImageData(evt.data.data, 0, 0);

	document.body.style.backgroundImage = 'url(' + canvas.toDataURL() + ')';
});


//send a data to the worker
rangerWorker.postMessage({
	rgb: rgbArray,
	type: 'polar',
	space: 'lab',
	channel: [0,1],
	max: [360, 100],
	min: [0, 100],
	data: imageData,
	id: 1
});
```

Worker gets all the same parameters as [`.renderRect`](#.renderRect) or [`.renderPolar`](#.renderPolar) whilst there are two additional options: `type` and `id`.

| Parameter | Type | Description |
|----|----|----|
| type | _string_ | A type of plot to render: `'rect'` or `'polar'`. |
| id | _number_ | A number of request to identify response. Returned unchanged. |


# Contribute

There are some things to do with this lib.

* At first, it needs implementing WebGL shaders version, or combining somehow with [color-space-canvas](https://github.com/rosskettle/color-space-canvas) module.
* At second, there’s no HUSL space in webworker available, because HUSL space requires intricated serialization of code for web-worker.
* Also it might need to add a triangular shape rendering.
* It’s a good idea to add server-side rendering as well.
* Asm-js calculation

So please fork, add fixes/features, make a PR. Color-ranger is an unlicensed project in that it is free to use or modify.


[![NPM](https://nodei.co/npm/color-ranger.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/color-ranger/)
