# decksh: a little language for deck markup

```decksh``` is a domain-specific language (DSL) for generating [```deck```](https://github.com/ajstarks/deck/blob/master/README.md) markup.

## Running

```decksh``` reads from the specified input, and writes deck markup to the specified output destination:

	$ decksh                   # input from stdin, output to stdout
	$ decksh -o foo.xml        # input from stdin, output to foo.xml
	$ decksh foo.sh            # input from foo.sh output to stdout
	$ decksh -o foo.xml foo.sh # input from foo.sh output to foo.xml
	
Typically, ```decksh``` acts as the head of a rendering pipeline:

	$ decksh text.sh | pdf -pagesize 1200,900 

## Example input

This deck script:

	// Example deck
	midx=50
	midy=50
	iw=640
	ih=480

	imfile="follow.jpg"
	imlink="https://budnitzbicycles.com"
	imscale=58
	dtop=87

	opts="-fulldeck=f -textsize 1  -xlabel=2  -barwidth 1.5"
	deck begin
		slide begin "white" "black"
			ctext "Deck elements" midx dtop 5
			cimage "follow.jpg" "Dreams" 72 midy iw ih imscale imlink
			textblock "Budnitz #1, Plainfield, NJ, May 10, 2015" 55 35 10 1 "serif" "white"

			// List
			blist 10 75 3
				li "text, image, list"
				li "rect, ellipse, polygon"
				li "line, arc, curve"
			elist

			// Graphics
			gy=10
			c1="red"
			c2="blue"
			c3="green"
			rect    15 gy 8 6              c1
			ellipse 27.5 gy 8 6            c2
			polygon "37 37 45" "7 13 10"   c3
			line    50 gy 60 gy 0.25       c1
			arc     70 gy 10 8 0 180 0.25  c2
			curve   80 gy 95 25 90 gy 0.25 c3


			// Chart
			chartleft=10
			chartright=45
			charttop=42
			chartbottom=28
			dchart -left chartleft -right chartright -top charttop -bottom chartbottom opts AAPL.d 
		slide end
	deck end

	
Produces:

![exampledeck](exampledeck.png)
	
Text, font, color, caption and link arguments follow Go convetions (surrounded by double quotes).
Colors are in rgb format ("rgb(n,n,n)"), or [SVG color names](https://www.w3.org/TR/SVG11/types.html#ColorKeywords).

Coordinates, dimensions, scales and opacities are floating point numbers ranging from from 0-100 
(they represent percentages on the canvas and percent opacity).  Some arguments are optional, and 
if omitted defaults are applied (black for text, gray for graphics, 100% opacity).

Canvas size and image dimensions are in pixels.

```id=<number>``` defines a constant, which may be then subtitited. For example:

	x=10
	y=20
	text "hello, world" x y 5


## Structure

Begin or end a deck.

	deck begin
	deck end
	
Begin, end a slide with optional background and text colors.
Specify the size of the canvas.

	slide begin [bgcolor] [fgcolor]
	slide end
	canvas w h
	
## Text 

Show left, centered, end or block-aligned text or a file's contents with 
optional font ("sans", "serif", "mono", or "symbol"), color and opacity.

Also, show blocks of code on a gray background.

	text       "text"     x y size       [font] [color] [opacity]
	ctext      "text"     x y size       [font] [color] [opacity]
	etext      "text"     x y size       [font] [color] [opacity]
	textfile   "filename" x y size       [font] [color] [opacity]
	textblock  "text"     x y width size [font] [color] [opacity]
	textcode   "filename" x y width size [color]
	
## Images 

Plain and captioned, with optional scales and links

	image  "file" x y width height [scale] [link]
	cimage "file" "caption" x y width height [scale] [link]
	
## Lists 
(plain, bulleted, and numbered)
	
	list   x y size [font] [color] [opacity]
	blist  x y size [font] [color] [opacity]
	nlist  x y size [font] [color] [opacity]

### list items, and ending the list

	li "text"
	elist
	
## Graphics

Rectangles, ellipses, squares and circles: specify the location and dimensions with optional color and opacity.

	rect    x y w h [color] [opacity]
	ellipse x y w h [color] [opacity]

	square  x y w   [color] [opacity]
	circle  x y w   [color] [opacity]

For polygons, specify the x and y coordinates as a series of numbers, with optional color and opacity.
	
	polygon "xcoords" "ycoords" [color] [opacity]

For lines specify the coordinates for the beginning and end points. For arc, specify the location of the center point, its width and height, and beginning and ending angles.
Curve is a quadratic bezier: specify the beginning location, the control point, and ending location.  Size, color and opacity are optional, and defaults are applied.

	line    x1 y1 x2 y2       [size] [color] [opacity]
	arc     x y w h a1 a2     [size] [color] [opacity]
	curve   x1 y1 x2 y2 x3 y3 [size] [color] [opacity]

## Charts

Run the [dchart](https://github.com/ajstarks/deck/blob/master/cmd/dchart/README.md) command with the specified arguments.

	dchart [args]

