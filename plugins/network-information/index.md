# Network Information

This plugin provides an implementation of an old version of the [Network Information API](http://www.w3.org/TR/2011/WD-netinfo-api-20110607/).
It provides information about the device's cellular and wifi connection, and whether the device has an internet connection.


# Connection

> The `connection` object, exposed via `navigator.connection`,  provides information about the device's cellular and wifi connection.

## Properties

- connection.type

## Constants

- Connection.UNKNOWN
- Connection.ETHERNET
- Connection.WIFI
- Connection.CELL_2G
- Connection.CELL_3G
- Connection.CELL_4G
- Connection.CELL
- Connection.NONE

## connection.type

This property offers a fast way to determine the device's network
connection state, and type of connection.

### Quick Example

    function checkConnection() {
        var networkState = navigator.connection.type;

        var states = {};
        states[Connection.UNKNOWN]  = 'Unknown connection';
        states[Connection.ETHERNET] = 'Ethernet connection';
        states[Connection.WIFI]     = 'WiFi connection';
        states[Connection.CELL_2G]  = 'Cell 2G connection';
        states[Connection.CELL_3G]  = 'Cell 3G connection';
        states[Connection.CELL_4G]  = 'Cell 4G connection';
        states[Connection.CELL]     = 'Cell generic connection';
        states[Connection.NONE]     = 'No network connection';

        alert('Connection type: ' + states[networkState]);
    }

    checkConnection();


### iOS Quirks

- iOS can't detect the type of cellular network connection.
    - `navigator.connection.type` is set to `Connection.CELL` for all cellular data.


# Network-related Events

## offline

The event fires when an application goes offline, and the device is
not connected to the Internet.

    document.addEventListener("offline", yourCallbackFunction, false);

### Details

The `offline` event fires when a previously connected device loses a
network connection so that an application can no longer access the
Internet.  It relies on the same information as the Connection API,
and fires when the value of `connection.type` becomes `NONE`.

Applications typically should use `document.addEventListener` to
attach an event listener once the `deviceready` event fires.

### Quick Example

    document.addEventListener("offline", onOffline, false);

    function onOffline() {
        // Handle the offline event
    }


### iOS Quirks

During initial startup, the first offline event (if applicable) takes at least a second to fire.


## online

This event fires when an application goes online, and the device
becomes connected to the Internet.

    document.addEventListener("online", yourCallbackFunction, false);

### Details

The `online` event fires when a previously unconnected device receives
a network connection to allow an application access to the Internet.
It relies on the same information as the Connection API,
and fires when the `connection.type` changes from `NONE` to any other
value.

Applications typically should use `document.addEventListener` to
attach an event listener once the `deviceready` event fires.

### Quick Example

    document.addEventListener("online", onOnline, false);

    function onOnline() {
        // Handle the online event
    }


### iOS Quirks

During initial startup, the first `online` event (if applicable) takes
at least a second to fire, prior to which `connection.type` is
`UNKNOWN`.
