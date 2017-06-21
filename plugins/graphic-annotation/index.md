# Graphical Annotation

This plugin allows to capture of free-hand drawing.

This plugin defines a global `navigator.handDrawTool` object.

Although the object is in the global scope, features provided by this plugin are not available until after the `deviceready` event.

    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
        console.log("navigator.handDrawTool is now available");
    }

## Methods

* __navigator.handDrawTool.record__: Show widget to capture drawings.
* __navigator.handDrawTool.edit__: Show widget to capture drawings with multi-page support.
* __navigator.handDrawTool.show__: Show captured contents in read-only multi-page mode.
* __navigator.handDrawTool.getSVG__: Convert graphics data to SVG.
* __navigator.handDrawTool.drawInContext__: Draw graphics data into a canvas.

### navigator.handDrawTool.record
Show the capture surface.

#### Syntax
`navigator.handDrawTool.record(callback, options)`

#### Parameters
* __callback__: A callback that will receive an opaque object that encapsulates graphic data. To get SVG data, call `getSVG()` by passing this object. In the case where browser does not support SVG (a HTML5 feature), call `drawInContext()` to draw the image on a canvas (still a HTML5 feature but more widely supported).
* __options__: An object which can contain following data members:
  * __mode__:	the capture mode, must be `annotation` (default) or `signature`. See remarks below.
  * __width__:	the width of capture area. Default is 100.
  * __height__:	the height of capture area. Default is 1/5 of width.
  * __edit__:	an object returned from the previous call of startRecording, if you want to edit old data.
  * __background__:	an URL to an image used as background. The image fill be stretched to full the capture area.
  * __allowScale__:	a boolean value which specifies whether zooming with pinch gestures is allowed or not. The default value is TRUE. Ignored for signature mode, which does not support zooming.

  Note that the provided width and height in options are used to calculate the capture area size ratio only.
  The capture area will expand as wide as it can while keep the height so that the given ratio is respected.

#### Remarks
This method works in two modes: `annotation` and `signature`. In `annotation` mode, many tools and colors are offered to users in composing their drawing. In `signature` mode, which is an optimized mode for taking signatures, no choice of tools or colors are available, and the interface behaves more like a dialog with OK and Cancel buttons.


### navigator.handDrawTool.edit
[_From 10.5 only_]

Show a capture surface with multi-page capability.

#### Syntax
`navigator.handDrawTool.edit(pages, annots, changeCallback, doneCallback, options)`

#### Parameters
* __pages__: An array of strings. Each element is either `null` or an URL to an image used as content of each page. The image will be stretched to full display area. The number of this array defines the number of pages.
* __annots__: An array or null. Each object is graphics data returned from `record()` or `edit()`. These graphics will be overlaid on top of page content. Elements of array may be null. If there are more elements in `annots` compared to `pages`, they will be ignored. If there are less, the last elements are considered as they were `null`.
* __changeCallback__: A callback which will be called everytime the content of a page changes. This callback will receive two arguments:
  * __index__: the index of edited page
  * __annot__: new annotation data.
* __doneCallback__: A callback which is called when user close the widget. This callback will receive one argument which is an integer 0-based index of the current page. 
* __options__: Provides optional parameters:
  * __aspectX__: A number which, together with aspectY, defines the ratio aspect of display area. Default ratio is taken from page’s content, or 1 if no content is available.
  * __aspectY__: A number which, together with aspectX, this define the ratio aspect of display area. Default ratio is taken page content, or 1 if no content is available.


### navigator.handDrawTool.show
[_From 10.5 only_]

Similar to edit() but show read-only UI.

#### Syntax
`navigator.handDrawTool.show(pages, annots, doneCallback, options)`

#### Parameters
* __pages__: An array of strings. Each element is either `null` or an URL to an image used as content of each page. The image will be stretched to full display area. The number of this array defines the number of pages.
* __annots__: An array or null. Each object is graphics data returned from `record()` or `edit()`. These graphics will be overlaid on top of page content. Elements of array may be null. If there are more elements in `annots` compared to `pages`, they will be ignored. If there are less, the last elements are considered as they were `null`.
* __doneCallback__: A callback which is called when user close the widget. This callback will receive one argument which is an integer 0-based index of the current page. 
* __options__: Provides optional parameters:
  * __aspectX__: A number which, together with aspectY, defines the ratio aspect of display area. Default ratio is taken from page’s content, or 1 if no content is available.
  * __aspectY__: A number which, together with aspectX, this define the ratio aspect of display area. Default ratio is taken page content, or 1 if no content is available.

### navigator.handDrawTool.getSVG
Generate SVG data from opaque graphic data returned by `record()` or `edit()`.

#### Syntax
`navigator.handDrawTool.getSVG(graphicData, width, height)`

#### Parameters
* __graphicData__: The opaque graphic data as returned by `record()` or `edit()`.
* __width__: (string|number) value for attribute `width` of `<svg>` element.
* __height__: (string|number) value for attribute `height` of `<svg>` element.

#### Compatibility note:
Prior to COTG 10.6, parameters `width` and `height` parameters must be numbers. From 10.6, these values are no longer interpreted and are passed as-is as attributes to `<svg>` element.

### navigator.handDrawTool.drawInContext
Render opaque graphic data into a Canvas.

#### Syntax
`navigator.handDrawTool.drawInContext(context, graphicData, width, height)`

#### Parameters
* __context__: The 2D context returned by the canvas.
* __graphicData__: The opaque graphic data as returned by `record()` or `edit()`.
* __width__: (number) the width of surface to render.
* __height__: (number) the height of surface to render.

#### Example
    var imgCanvas = document.createElement('canvas');
    imgCanvas.setAttribute('width', imgWidth);
    imgCanvas.setAttribute('height', imgHeight);
    navigator.handDrawTool.drawInContext(imgCanvas.getContext('2d'), data, imgWidth, imgHeight);
