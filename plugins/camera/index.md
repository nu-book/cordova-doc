# Camera

This plugin defines a global `navigator.camera` object, which provides an API for taking pictures and for choosing images from the system's image library.

Although the object is attached to the global scoped `navigator`, it is not available until after the `deviceready` event.

    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
        console.log(navigator.camera);
    }

## navigator.camera.getPicture

Takes a photo using the camera, or retrieves a photo from the device's
image gallery.  The image is passed to the success callback as a
base64-encoded `String`, or as the URI for the image file.  The method
itself returns a `CameraPopoverHandle` object that can be used to
reposition the file selection popover.

    navigator.camera.getPicture(cameraSuccess, cameraError, cameraOptions);

### Description

The `camera.getPicture` function opens the device's default camera
application that allows users to snap pictures. This behavior occurs
by default, when `Camera.sourceType` equals
`Camera.PictureSourceType.CAMERA`.  Once the user snaps the photo, the
camera application closes and the application is restored.

If `Camera.sourceType` is `Camera.PictureSourceType.PHOTOLIBRARY` or
`Camera.PictureSourceType.SAVEDPHOTOALBUM`, then a dialog displays
that allows users to select an existing image.  The
`camera.getPicture` function returns a `CameraPopoverHandle` object,
which can be used to reposition the image selection dialog, for
example, when the device orientation changes.

The return value is sent to the `cameraSuccess` callback function, in
one of the following formats, depending on the specified
`cameraOptions`:

- A `String` containing the base64-encoded photo image.

- A `String` representing the image file location on local storage (default).

You can do whatever you want with the encoded image or URI, for
example:

- Render the image in an `<img>` tag, as in the example below

- Save the data locally (`LocalStorage`, [Lawnchair](http://brianleroux.github.com/lawnchair/), etc.)

- Post the data to a remote server

__NOTE__: Photo resolution on newer devices is quite high, to avoid common
memory problems, set `Camera.destinationType` to `FILE_URI` rather
than `DATA_URL`.


### Android Quirks

- Android uses intents to launch the camera activity on the device to capture
images, and on phones with low memory, the COTG app may be killed.  In this
scenario, the image will be lost.

- `cameraDirection` option may not work, depending which camera app is used.

### iOS Quirks

Including a JavaScript `alert()` in either of the callback functions
can cause problems.  Wrap the alert within a `setTimeout()` to allow
the iOS image picker or popover to fully close before the alert
displays:

    setTimeout(function() {
        // do your thing here!
    }, 0);

### Windows Quirks
`cameraDirection` option is not supported

### Example

Take a photo and retrieve it as a base64-encoded image:

    navigator.camera.getPicture(onSuccess, onFail, { quality: 50,
        destinationType: Camera.DestinationType.DATA_URL
    });

    function onSuccess(imageData) {
        var image = document.getElementById('myImage');
        image.src = "data:image/jpeg;base64," + imageData;
    }

    function onFail(message) {
        alert('Failed because: ' + message);
    }

Take a photo and retrieve the image's file location:

    navigator.camera.getPicture(onSuccess, onFail, { quality: 50,
        destinationType: Camera.DestinationType.FILE_URI });

    function onSuccess(imageURI) {
        var image = document.getElementById('myImage');
        image.src = imageURI;
    }

    function onFail(message) {
        alert('Failed because: ' + message);
    }

## CameraOptions

Optional parameters to customize the camera settings.

    { quality : 75,
      destinationType : Camera.DestinationType.DATA_URL,
      sourceType : Camera.PictureSourceType.CAMERA,
      allowEdit : true,
      encodingType: Camera.EncodingType.JPEG,
      targetWidth: 100,
      targetHeight: 100,
      popoverOptions: CameraPopoverOptions};

### Options

- __quality__: Quality of the saved image, expressed as a range of 0-100, where 100 is typically full resolution with no loss from file compression. The default is 50. _(Number)_ (Note that information about the camera's resolution is unavailable.)

- __destinationType__: Choose the format of the return value. The default is FILE_URI. Defined in `Camera.DestinationType` _(Number)_

        Camera.DestinationType = {
            DATA_URL : 0,      // Return image as base64-encoded string
            FILE_URI : 1,      // Return image file URI
            NATIVE_URI : 2     // Return image native URI (e.g., assets-library:// on iOS or content:// on Android)
        };

- __sourceType__: Set the source of the picture. The default is CAMERA. Defined in `Camera.PictureSourceType` _(Number)_

        Camera.PictureSourceType = {
            PHOTOLIBRARY : 0,
            CAMERA : 1,
            SAVEDPHOTOALBUM : 2
        };

- __allowEdit__: Allow simple editing of image before selection. _(Boolean)_ Default is false. Ignored if __allowDeskew__ is true.

- __encodingType__: Choose the  returned image file's encoding. Default is JPEG. Defined in `Camera.EncodingType` _(Number)_

        Camera.EncodingType = {
            JPEG : 0,               // Return JPEG encoded image
            PNG : 1                 // Return PNG encoded image
        };

- __targetWidth__: Width in pixels to scale image. Must be used with __targetHeight__.  Aspect ratio remains constant. _(Number)_ Default is no limit.

- __targetHeight__: Height in pixels to scale image. Must be used with __targetWidth__. Aspect ratio remains constant. _(Number)_ Default is no limit.

- __mediaType__: Set the type of media to select from.  Only works when `PictureSourceType` is `PHOTOLIBRARY` or `SAVEDPHOTOALBUM`. Defined in `Camera.MediaType` _(Number)_

        Camera.MediaType = {
            PICTURE: 0,    // allow selection of still pictures only. DEFAULT. Will return format specified via DestinationType
            VIDEO: 1,      // allow selection of video only, WILL ALWAYS RETURN FILE_URI
            ALLMEDIA : 2   // allow selection from all media types
        };
        
- __cameraDirection__: (From version 10.8) Choose the camera to use (front- or back-facing). Defined in `Camera.Direction` _(Number)_

        Camera.Direction = {
            BACK: 0,
            FRONT: 1
        };
        
    When this option is not provided, the plugin will default the back-facing camera for the first use, but will remember the last camera user changed to and will use the same.
    By providing this option, this behavior is disabled and the requested camera will be used.
        

- __correctOrientation__: Rotate the image to correct for the orientation of the device during capture. _(Boolean)_

- __popoverOptions__: iOS-only options that specify popover location in iPad.  Defined in `CameraPopoverOptions`.

- __allowDeskew__: Displays an UI assisting user to perform a perspective cropping and image processing after taking picture. This option will have effect only if mediaType is PICTURE. _(Boolean)_ Default is false.

- __colorspace__: In the image processing step, user is presented by default with 3 modes that she can choose, Black White, Grayscale, and Color. In order to simplify the UI, this option can be specified so that only one mode is available, the value is an enum defined as following:

        Camera.Colorspace = {
            BW: 0,        // Result image will be in black & white
            GRAYSCALE: 1, // Result image will be in 256 grayscale levels
            COLOR : 2     // Result image will be in RGB
        };

- __stampFormat__: Stamp the photo with given date format pattern. For _sourceType_ CAMERA only. The following pattern letters are defined.

  | Pattern | Description                                       | Examples
  |---------|---------------------------------------------------|---------------------------
  | yy	    | Year, from 00 to 99.                              | 2009-06-15 13:45:30 -> 09
  | yyyy    | Year as a four-digit number.                      | 2009-06-15 13:45:30 -> 1909
  | M       | Month, from 1 through 12.                         | 2009-06-15 13:45:30 -> 6
  | MM      | Month, from 01 through 12.                        | 2009-06-15 13:45:30 -> 06
  | MMM     | Abbreviated name of the month.                    | 2009-06-15 13:45:30 -> Jun (en-US)
  | MMMM    | Full name of the month.                           | 2009-06-15 13:45:30 -> June (en-US)
  | d       | Day of the month, from 1 through 31.              | 2009-06-01 13:45:30 -> 1 <br/> 009-06-15 13:45:30 -> 15
  | dd      | Day of the month, from 01 through 31.             | 2009-06-01 13:45:30 -> 01 <br/> 009-06-15 13:45:30 -> 15
  | EEE     | Abbreviated name of the day of the week.          | 2009-06-15 13:45:30 -> Mon (en-US)
  | EEEE    | Full name of the day of the week.                 | 2009-06-15 13:45:30 -> Monday (en-US)
  | a       | AM/PM designator.                                 | 2009-06-15 13:45:30 -> PM (en-US)
  | H       | Hour, using a 24-hour clock from 0 to 23.         | 2009-06-15 01:45:30 -> 1
  | HH      | Hour, using a 24-hour clock from 00 to 23.        | 2009-06-15 01:45:30 -> 01
  | h       | Hour, using a 12-hour clock from 1 to 12.         | 2009-06-15 13:45:30 -> 1
  | hh      | Hour, using a 12-hour clock from 01 to 12.        | 2009-06-15 13:45:30 -> 01
  | m       | Minute, from 0 through 59.                        | 2009-06-15 01:09:30 -> 9
  | mm      | Minute, from 00 through 59.                       | 2009-06-15 01:09:30 -> 09
  | s       | Second, from 0 through 59.                        | 2009-06-15 13:45:07 -> 7
  | ss      | Second, from 00 through 59.                       | 2009-06-15 13:45:07 -> 07
  | X       | Hours offset from UTC.                            | 2009-06-15 13:45:30-07:30 -> -07
  | XX      | Hours and minutes offset from UTC.                | 2009-06-15 13:45:30-07:30 -> -0730
  | XXX     | Hours and minutes offset from UTC with separator. | 2009-06-15 13:45:30-07:30 -> -07:30
  | l       | Localized date (1).                               | 2009-06-15 13:45:30 -> 6/15/2009 (en-US)
  | ll      | Localized date (1).                               | 2009-06-15 13:45:30 -> Jun 15, 2009 (en-US)
  | lll     | Localized date (1).                               | 2009-06-15 13:45:30 -> June 15, 2009 (en-US)
  | llll    | Localized date (1).                               | 2009-06-15 13:45:30 -> Monday, June 15, 2009 (en-US)
  | L       | Localized date and time (2).                      | 2009-06-15 13:45:30 -> 6/15/2009 1:45 PM (en-US)
  | LL      | Localized date and time (2).                      | 2009-06-15 13:45:30 -> Jun 15, 2009 1:45:30 PM (en-US)
  | LLL     | Localized date and time (2).                      | 2009-06-15 13:45:30 -> June 15, 2009 1:45:30 PM EDT (en-US)
  | LLLL    | Localized date and time (2).                      | 2009-06-15 13:45:30 -> Monday, June 15, 2009 1:45 PM Eastern Daylight Time (en-US)
  
  All other characters from 'A' to 'Z' and from 'a' to 'z' are reserved. Text can be quoted using single quotes (') to avoid interpretation. "''" represents a single quote.
  
  (1) This is the pattern returned by navigator.globalization.getDatePattern() with `selector: "date"` and `formatLength` is `short`, `medium`, `long` or `full` for l, ll, lll, or llll respectively. These patterns **must** be standalone, that is you cannot use them together with other pattern or any other character.

  (2) This is the pattern returned by navigator.globalization.getDatePattern() with `selector: "date and time"` and `formatLength` is `short`, `medium`, `long` or `full` for L, LL, LLL, or LLLL respectively. These patterns **must** be standalone, that is you cannot use them together with other pattern or any other character.
  
  Default is empty which means photos are not stamped.


- __stampAlign__: Specify the position of the stamp. The position is specified by combining horizontal alignment (left, center, right) with vertical alignment (top, middle, bottom) and joining them with vertical bar ('|'). For example, `top|right`, which is the same as `right|top`, specifies that stamping text will be aligned at top right corner. Default is `bottom|left`.

- __stampFontSize__: One of `small`, `medium`, or `large` to specify the size of stamping text. Default is `medium`.

### Android Quirks

- Images are saved in a cache directory specific to each form, and will be removed when user removes the form from his library.
- `Camera.PictureSourceType.PHOTOLIBRARY` and `Camera.PictureSourceType.SAVEDPHOTOALBUM` both display the same photo album.

### iOS Quirks

- Images are saved in a cache directory specific to each form, and will be removed when user removes the form from his library.

### Windows Quirks

- Images are saved in a cache directory specific to each form, and will be removed when user removes the form from his library.
- Prior to version COTG 3.0, cropping (option allowEdit) is not supported and ignored when running on devices in Mobile device family (telephones) or when picking image from library. From version 3.0, cropping is supported as expected.


## CameraError

onError callback function that provides an error message.

    function(message) {
        // Show a helpful message
    }

### Parameters

- __message__: The message is provided by the device's native code. _(String)_


## cameraSuccess

onSuccess callback function that provides the image data.

    function(imageData) {
        // Do something with the image
    }

### Parameters

- __imageData__: Base64 encoding of the image data, _or_ the image file URI, depending on `cameraOptions` in effect. _(String)_

### Example

    // Show image
    //
    function cameraCallback(imageData) {
        var image = document.getElementById('myImage');
        image.src = "data:image/jpeg;base64," + imageData;
    }


## CameraPopoverHandle

A handle to the popover dialog created by `navigator.camera.getPicture`.

### Methods

- __setPosition__: Set the position of the popover.

### Supported Platforms

- iOS

### setPosition

Set the position of the popover.

__Parameters__:

- `cameraPopoverOptions`: the `CameraPopoverOptions` that specify the new position

### Example

     var cameraPopoverHandle = navigator.camera.getPicture(onSuccess, onFail,
         { destinationType: Camera.DestinationType.FILE_URI,
           sourceType: Camera.PictureSourceType.PHOTOLIBRARY,
           popoverOptions: new CameraPopoverOptions(300, 300, 100, 100, Camera.PopoverArrowDirection.ARROW_ANY)
         });

     // Reposition the popover if the orientation changes.
     window.onorientationchange = function() {
         var cameraPopoverOptions = new CameraPopoverOptions(0, 0, 100, 100, Camera.PopoverArrowDirection.ARROW_ANY);
         cameraPopoverHandle.setPosition(cameraPopoverOptions);
     }


## CameraPopoverOptions

iOS-only parameters that specify the anchor element location and arrow
direction of the popover when selecting images from an iPad's library
or album.

    { x : 0,
      y :  32,
      width : 320,
      height : 480,
      arrowDir : Camera.PopoverArrowDirection.ARROW_ANY
    };

### CameraPopoverOptions

- __x__: x pixel coordinate of screen element onto which to anchor the popover. _(Number)_

- __y__: y pixel coordinate of screen element onto which to anchor the popover. _(Number)_

- __width__: width, in pixels, of the screen element onto which to anchor the popover. _(Number)_

- __height__: height, in pixels, of the screen element onto which to anchor the popover. _(Number)_

- __arrowDir__: Direction the arrow on the popover should point.  Defined in `Camera.PopoverArrowDirection` _(Number)_

            Camera.PopoverArrowDirection = {
                ARROW_UP : 1,        // matches iOS UIPopoverArrowDirection constants
                ARROW_DOWN : 2,
                ARROW_LEFT : 4,
                ARROW_RIGHT : 8,
                ARROW_ANY : 15
            };

Note that the size of the popover may change to adjust to the
direction of the arrow and orientation of the screen.  Make sure to
account for orientation changes when specifying the anchor element
location.

## navigator.camera.cleanup

Removes intermediate photos taken by the camera from temporary
storage.

    navigator.camera.cleanup( cameraSuccess, cameraError );

### Description

Removes intermediate image files that are kept in temporary storage
after calling `camera.getPicture`. Applies only when the value of
`Camera.sourceType` equals `Camera.PictureSourceType.CAMERA` and the
`Camera.destinationType` equals `Camera.DestinationType.FILE_URI`.

### Supported Platforms

- iOS

### Example

    navigator.camera.cleanup(onSuccess, onFail);

    function onSuccess() {
        console.log("Camera cleanup success.")
    }

    function onFail(message) {
        alert('Failed because: ' + message);
    }

