# JSXGraph extension for [Numbas](https://www.numbas.org.uk/)

This extension provides the [JSXGraph](https://jsxgraph.org) library and functions to create diagrams inside a Numbas question.

## JME functions

### `jsxgraph(width, height, [boundingBox], objects, [options])`

Create a JSXGraph board.

* `width: number` - the displayed width in pixels of the board.
* `height: number` - the displayed height in pixels of the board.
* `boundingBox: [number, number, number, number]` - an optional list of four numbers defining the bounding box of the diagram, in the format `[x1,y1,x2,y2]`. The first point is mappted to the top-left of the board, and the second point to the bottom-right.
* `objects: list or dict` - a list of object definitions, or a dictionary mapping object names to definitions.
* `options: dict` - a dictionary of options for the board. See [the documentation for JSXGraph.initBoard](https://jsxgraph.uni-bayreuth.de/docs/symbols/JXG.JSXGraph.html#.initBoard)

An object definition is a list in the format `[type, parents, attribute]`.

* `type: string` - the type of object to create. Available types are listed in [the 'Elements' section of the JSXGRaph' documentation](https://jsxgraph.uni-bayreuth.de/docs/).
* `parents: list` - a list of 'parent' values for the object. The format of this list depends on the type of the object being created. Any values of data type `expression` are replaced with functions which take a single parameter.
* `attributes: dict` - a dictionary of attributes for the object. The available attributes dpeend on the type of the object.

JSXGraph will often interpret string values in the `parents` list as JavaScript expressions. You can refer to other objects by name.
Alternately, you can provide JME `expression` values to evaluate expressions in terms of JME variables and functions. JSXGraph objects are not available in these expressions, and they're quite a bit slower than JavaScript.

#### Example

This creates a board with a line and a glider point on that line:

```
jsxgraph(
    500,500,
    [
        "l": ['line', [[0,1], [1,2]]],
        "p": ['glider', ['l']]
    ]
)
```

This creates a board with a polar plot of the function `r(phi) = sin(5phi)` centred at the origin:

```
jsxgraph(
    500,500,
    [
        ['curve', [expression('sin(5phi)'),[0,0],0,2pi], ["curveType": "polar"]]
    ]
)
```

### `jessiecode(width, height, [boundingBox], script, [options])`

Create a JSXGraph board defined by a JessieCode script.

JessieCode is a languaged designed for efficiently creating diagrams in JSXGraph. 
There is [some documentation about JessieCode syntax](https://jsxgraph.org/wp/docs_jessiecode/). The available object types and their construction options are the same as in the [JavaScript API](https://jsxgraph.org/docs/).

* `width: number` - the displayed width in pixels of the board.
* `height: number` - the displayed height in pixels of the board.
* `boundingBox: [number, number, number, number]` - an optional list of four numbers defining the bounding box of the diagram, in the format `[x1,y1,x2,y2]`. The first point is mappted to the top-left of the board, and the second point to the bottom-right.
* `script: string` - a JessieCode script defining the objects in the board. 
* `options: dict` - a dictionary of options for the board. See [the documentation for JSXGraph.initBoard](https://jsxgraph.uni-bayreuth.de/docs/symbols/JXG.JSXGraph.html#.initBoard)

#### Example

This creates a board with two points and a dashed line between them, and no background grid or axes:

```
jessiecode(
    500,500,
    """
        A = point(0,1);
        B = point(4,0);

        line(A,B) << dash: 4 >>;
    """,
    [
        "axis": false
    ]
)
```

## JavaScript functions

All JavaScript functions are properties of the object `Numbas.extensions.jsxgraph`.

### `makeBoardPromise(width, height, options)`

Make a JSXGraph board with the given dimensions and options, and attach it to a `<div>` element.
The board is only initialised once the container element has been attached to the page.

An object of the format `{element, promise}` is returned. 
The `element` is a container `<div>` element that the board will be created under.
The `promise` resolves to the JSXGraph Board object once the container has been attached to the page and the board initialised.

The `options` object is passed to `JSXGraph.initBoard`, with some defaults applied.
The default options are:

```
{
    boundingBox:[-5,5,5,-5],
    showCopyright:false, 
    showNavigation:false, 
    axis:true
}
```

#### Example

This creates a board with the default options and a single point at the coordinates (1,2).

```
var result = Numbas.extensions.jsxgraph.makeBoardPromise(500,500);
result.promise.then(function(board) {
    board.create('point',[1,2]);
});
return result.element;
```


### `makeBoard(width, height, options)`

Make a JSXGraph board with the given dimensions and options, and attach it to a `<div>` element.
The div element is returned. The JSXGraph board object is available as `div.board`.

The `options` object is passed to `JSXGraph.initBoard`, with some defaults applied.
The default options are the same as for `makeBoardPromise`.

**Note**: This function initialises the board immediately, and is kept for backwards compatibility.
It's better to wait until the container element is attached to the page, using `makeBoardPromise`.

#### Example

This creates a board with the default options and a single point at the coordinates (1,2).

```
var div = Numbas.extensions.jsxgraph.makeBoard(500,500);
div.board.create('point',[1,2]);
return div;
```
