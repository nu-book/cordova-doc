# pause

The event fires when an application is put into the background.

    document.addEventListener("pause", yourCallbackFunction, false);

## Details

The `pause` event fires when the native platform puts the application
into the background, typically when the user switches to a different
application.

Applications typically should use `document.addEventListener` to
attach an event listener once the `[deviceready](events.deviceready.html)` event fires.

## Quick Example

    document.addEventListener("pause", onPause, false);

    function onPause() {
        // Handle the pause event
    }

## Full Example

    <!DOCTYPE html>
    <html>
      <head>
        <title>Pause Example</title>

        <script type="text/javascript" charset="utf-8" src="cordova.js"></script>
        <script type="text/javascript" charset="utf-8">

        // Wait for device API libraries to load
        //
        function onLoad() {
            document.addEventListener("deviceready", onDeviceReady, false);
        }

        // device APIs are available
        //
        function onDeviceReady() {
            document.addEventListener("pause", onPause, false);
        }

        // Handle the pause event
        //
        function onPause() {
        }

        </script>
      </head>
      <body onload="onLoad()">
      </body>
    </html>

## iOS Quirks

In the `pause` handler, any calls to the Cordova API or to native
plugins that go through Objective-C do not work, along with any
interactive calls, such as alerts or `console.log()`. They are only
processed when the app resumes, on the next run loop.

The iOS-specific `resign` event is available as an alternative to
`pause`, and detects when users enable the __Lock__ button to lock the
device with the app running in the foreground.  If the app (and
device) is enabled for multi-tasking, this is paired with a subsequent
`pause` event, but only under iOS 5. In effect, all locked apps in iOS
5 that have multi-tasking enabled are pushed to the background.  For
apps to remain running when locked under iOS 5, disable the app's
multi-tasking by setting
[UIApplicationExitsOnSuspend](http://developer.apple.com/library/ios/#documentation/general/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html)
to `YES`. To run when locked on iOS 4, this setting does not matter.
