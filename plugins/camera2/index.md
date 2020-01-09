# Camera 2

This plugin defines a global `navigator.camera2` object, which provides an API for taking pictures with seamless end-user experience.

This plugin is provided as an internative to default Camera plugin in order to offer better UX for specific use-cases.
* Photo taking process is implemented directly by the app instead of relying on plateform-dependent UI or in case of Android, external third-party app. Thus, this provides an uniform UX on all platforms and devices.
* Ability to take multiples photos without interrupting by UI switch or confirmation dialogs. Optionally, Javascript can be notified after each capture.
* Ability to generate thumbnails at the same time the capture is done.
* Provide WYSIWYG date stamping.

Although the object is attached to the global scoped `navigator`, it is not available until after the `deviceready` event.

    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
        console.log(navigator.camera2);
    }

## navigator.camera2.takePictures

Takes one or many photos using the camera.

    navigator.camera2.takePictures(completionCallback, progressCallback, errorCallback, options);

### Parameters

* __completionCallback__: If provided, this must be a function which will be called after the whole capture process is completed, i.e. user terminates the process or the photo count limit is reached. This function will get a string array as argument. Each element in the array is an URL, location of JPEG file.
* __progressCallback__: If provided, this must be a function which will be called after each photo is taken. The argument to this function is the URL location of JPEG file.
* __errorCallback__: If provided, this must be a function which will be called when an error occurs. The argument is a string which contains error message.
* __options__: If provided, this must be an object with entries described below.

#### Options

- __targetWidth__ _(integer)_: If provided, this defines the maximal width in pixels. If the resulting image is larger than this size, it will be scaled down to satisfy `targetWidth` and `targetHeight` requirements but by preserving image ratio aspect. If `0` (default) or negative value is provided, it's interpreted as _no limit_.

- __targetHeight__ _(integer)_: If provided, this defines the maximal height in pixels. If the resulting image is taller than this size, it will be scaled down to satisfy `targetWidth` and `targetHeight` requirements but by preserving image ratio aspect. If `0` (default) or negative value is provided, it's interpreted as _no limit_.

- __quality__ _(integer)_: Quality of the saved image, expressed as a range of 0-100, where 100 is typically full resolution with no loss from file compression. The default is `50`.

- __limit__ _(integer)_: The maximum number of photos to be taken. Default is 0 which is no limit.

- __cameraDirection__: (From version 10.8) Choose the camera to use (front- or back-facing). Defined in `Camera.Direction` _(Number)_

        Camera.Direction = {
            BACK: 0,
            FRONT: 1
        };
        
    When this option is not provided, the plugin will default the back-facing camera for the first use, but will remember the last camera user changed to and will use the same.
    By providing this option, this behavior is disabled and the requested camera will be used.
        

- __fastCapture__ _(boolean)_: If `fast capture` mode is enabled. In fast capture mode, user does not need to confirm each photo taken. Default is `true`

- __stampFormat__ _(string)_: Stamp the photo with given date format pattern. The following pattern letters are defined.

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


- __stampAlign__ _(string)_: Specify the position of the stamp. The position is specified by combining horizontal alignment (left, center, right) with vertical alignment (top, middle, bottom) and joining them with vertical bar ('|'). For example, `top|right`, which is the same as `right|top`, specifies that stamping text will be aligned at top right corner. Default is `bottom|left`.

- __stampFontSize__ _(string)_: One of `small`, `medium`, or `large` to specify the size of stamping text. Default is `medium`.

- __thumbWidth__ _(integer)_: Defines the maximum width of thumbnail to be generated. Default is `0`. A thumbnail will be generated for each photo only if either `thumbWidth` or `thumbHeight` is provided with a positive value.
- __thumbHeight__ _(integer)_: Defines the maximum height of thumbnail to be generated. Default is `0`. A thumbnail will be generated for each photo only if either `thumbWidth` or `thumbHeight` is provided with a positive value.
- __thumbExtension__ _(string)_: The file extension to use for thumbnail image. This extension will be appended to original photo file path to form the path of thumbnail. Default is `.thumb`.


### Full Example

    <html>
    <head>
    <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
    <script>(function() {

    function deleteFile(url) {
        resolveLocalFileSystemURL(url, function(entry) { entry.isFile && entry.remove() });
    }

    function getNumber(str, defaultVal) {
        return str ? parseInt(str) : defaultVal;
    }

    function takePictures(pickFile)
    {
        var imgWidth = $('#camera2-width').val();
        var imgHeight = $('#camera2-height').val();
        var imgQuality = $('#camera2-quality').val();
        var limit =  getNumber($('#camera2-count-limit').val(), 0)
        var fastMode = !$('#camera2-confirm-each').prop('checked');
        var stampFormat = $('#camera2-stamp-format').val();
        var stampAlign = $('#camera2-stamp-align').val();
        var stampSize = $('#camera2-stamp-size').val();
        var thumbHeight = $('#camera2-with-thumb').prop('checked') ? 100 : 0;
        navigator.camera2.takePictures(
            // on success
            function(photos) {
                for (var i = 0; i < photos.length; ++i) {
                    addPhoto(photos[i]);
                    if (thumbHeight > 0) {
                        addPhoto(photos[i] + '.thumb')
                    }
                }
            },
            null, // ignore progress
            // on error
            function(error) {
                if (error != 'Did not complete!') {
                    console.log('Camera Plugin Error: ' + error);
                }
            },
            // options
            {   targetWidth: imgWidth,
                targetHeight: imgHeight,
                quality: imgQuality,
                limit: limit,
                fastCapture: fastMode,
                stampFormat: stampFormat,
                stampAlign: stampAlign,
                stampFontSize: stampSize,
                thumbHeight: thumbHeight
            }
        );
    }

    function showViewer(clicked) {
        var photos = getAllPhotoSrc();
        navigator.handDrawTool.show(photos, null, null, { startIndex: photos.indexOf(clicked) });
    }

    function getAllPhotoSrc() {
        return $('#camera2-photo-list>input').map(function(i, e) { return e.value; }).toArray();
    }

    var photoIndex = 0;
    function addPhoto(src) {
        photoIndex++;
        var input = $('<input/>', { type: 'hidden', name: 'camera2-photo-' + photoIndex, value: src });
        var wrapper = $('<div/>').addClass('camera2-photo-wrapper');
        var img = $('<img/>', { src: src }).addClass('camera2-limited-size').click(function() {
            showViewer(src);
        });
        var delBut = $('<a/>', { href: '#' }).addClass('camera2-delete-icon').html('&nbsp;X&nbsp;').click(function() {
            wrapper.remove();
            input.remove();
            deleteFile(src);
            return false;
        });
        wrapper.append(img).append(delBut);
        $('#camera2-photo-list').append(wrapper).append(input);
    }

    window.addEventListener('savestate', function(e) {
        e.detail.state['camera2-photos'] = getAllPhotoSrc();
    }, false);

    window.addEventListener('restorestate', function(e) {
        var photos = e.detail.state['camera2-photos'];
        if (photos) {
            $.each(photos, function(i, e) {
                addPhoto(e);
            });
        }
    }, false);

    $(document).ready(function() {
        $('#camera2-take').click(function() { takePictures() });

        $('#camera2-stamp').change(function() {
            $('#camera2-stamp-format').val($(this).val());
        });
    });

    })()
    </script>
    </head>
    <body>
    <div class="camera2-parameters">
        <div>Max Width: <select id="camera2-width" name="camera2-width"><option value="400" >400</option><option value="1200" selected>1200</option><option value="-1">No limit</option></select></div>
        <div>Max Height: <select id="camera2-height" name="camera2-height"><option value="400" >400</option><option value="1200" selected>1200</option><option value="-1">No limit</option></select></div>
        <div>Quality: <select id="camera2-quality" name="camera2-quality"><option value="50" selected>50</option><option value="80">80</option><option value="100">100</option></select></div>
        <div>Confirm Each Photo: <input type="checkbox" id="camera2-confirm-each" name="camera2-confirm-each"/></div>
        <div>Generate Thumbnail: <input type="checkbox" id="camera2-with-thumb" name="camera2-with-thumb"/></div>
        <div>Limit: <input id="camera2-count-limit" name="camera2-count-limit" type="number" value="1000" min="0"/></div>
        <div>Time Stamp: <select id="camera2-stamp" name="camera2-stamp"><option value="" selected>None</option><option value="L">Localized short</option><option value="LL">Localized long</option><option value="EEEE dd MMMM yyyy HH:mm">Custom Format</option></select> <input type="text" id="camera2-stamp-format" name="camera2-stamp-format"/></div>
        <div>Stamp Position: <select id="camera2-stamp-align" name="camera2-stamp-align"> 
                <option value="" selected>Default</option>
                <option value="left|top">Left Top</option>
                <option value="center|top">Center Top</option>
                <option value="top|right">Right Top</option>
                <option value="bottom|left">Left Bottom</option>
                <option value="center|bottom">Center Bottom</option>
                <option value="right|bottom">Right Bottom</option>
                </select></div>
        <div>Stamp Font Size: <select id="camera2-stamp-size" name="camera2-stamp-size"> 
                <option value="" selected>Default</option>
                <option value="small">Small</option>
                <option value="medium">Medium</option>
                <option value="large">Large</option>
                </select></div>
    </div>
    <input type="button" id="camera2-take" value="Take Pictures"/>
    <br/><br/>
    <div id="camera2-photo-list" class="floater-wrapper"></div>
    </body>
    </html>

