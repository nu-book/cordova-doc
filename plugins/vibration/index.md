# Vibration

This plugin aligns with the W3C vibration specification http://www.w3.org/TR/vibration/

This plugin provides a way to vibrate the device.

This plugin defines global objects including `navigator.vibrate`.

Although in the global scope, they are not available until after the `deviceready` event.

    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
        console.log(navigator.vibrate);
    }

## vibrate (recommended)

This function has three different functionalities based on parameters passed to it.

### Standard vibrate

Vibrates the device for a given amount of time.

    navigator.vibrate(time)

or

    navigator.vibrate([time])


-__time__: Milliseconds to vibrate the device. _(Number)_

#### Example

    // Vibrate for 3 seconds
    navigator.vibrate(3000);

    // Vibrate for 3 seconds
    navigator.vibrate([3000]);

#### iOS Quirks

- __time__: Ignores the specified time and vibrates for a pre-set amount of time.

    navigator.vibrate(3000); // 3000 is ignored

### Vibrate with a pattern (Android only)
Vibrates the device with a given pattern

    navigator.vibrate(pattern);   

- __pattern__: Sequence of durations (in milliseconds) for which to turn on or off the vibrator. _(Array of Numbers)_

#### Example

    // Vibrate for 1 second
    // Wait for 1 second
    // Vibrate for 3 seconds
    // Wait for 1 second
    // Vibrate for 5 seconds
    navigator.vibrate([1000, 1000, 3000, 1000, 5000]);

### Cancel vibration (not supported in iOS)

Immediately cancels any currently running vibration.

    navigator.vibrate(0)

Passing in a parameter of 0 will cancel any vibrations.
