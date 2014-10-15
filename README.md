## Color range

Render background for a color channel/space. Intended for color pickers or gradient generation.


```js
var range = require('color-range');
var Color = require('color');
var c = new Color('green');

element.style.background = range.rectangular.red.green(c, 'right');
```

## API

#### Rendering methods:

`.linear[space](color, direction)` — get linear range background based on passed color for the space.

`.rectangular[spaceH][spaceV](color, direction)` — get rectangular range based on passed color for the spaceA and spaceB.

`.circular[space](color, direction)` — get circular range

`.polar[spaceR][spaceA](color, direction)` — get polar background

`.triangular[spaceH][spaceV](color, direction)` — get triangular background

#### Helpers:

`.grad(direction?, [color1, color2, ...colorN])` — render even linear gradient based on colors passed.

`.interpolate(colorA, colorB, number, space)` — return list of @number colors between colorA and color B interpolated by @space.


#### Options:

Set options any time via `range.options`

`direction` — default direction of gradient
`space` — default color space to use as a basic
`gridColor` — alpha grid color
`gridSize` — alpha grid cell size


## Linear backgrounds

* red
* green
* blue
* hue
* saturation
* lightness
* saturationv
* value
* whiteness
* blackness
* L
* a
* b
* cyan
* magenta
* yellow
* black, kyan
* alpha, opacity


## Rectangular backgrounds

* red
	* green
	* blue
* green
	* red
	* blue
* hue
	* saturation
	* saturationv
	* lightness
	* value/brightness
* saturation
	* lightness
	* value/brightness
	* hue
* L
	* a
	* b

A components of each pair may be swapped.


## Circular backgrounds

All the same as a linear ones.


## Radial backgrounds

All the same as rectangular ones.


## Unlicensed