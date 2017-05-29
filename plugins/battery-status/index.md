# Battery Status

This plugin provides an implementation of an old version of the [Battery Status Events API](http://www.w3.org/TR/2011/WD-battery-status-20110915/).

It adds the following three `window` events:

* batterystatus
* batterycritical
* batterylow

## batterystatus

This event fires when the percentage of battery charge changes by at
least 1 percent, or if the device is plugged in or unplugged.

The battery status handler is passed an object that contains two
properties:

- __level__: The percentage of battery charge (0-100). _(Number)_

- __isPlugged__: A boolean that indicates whether the device is plugged in. _(Boolean)_

Applications typically should use `window.addEventListener` to
attach an event listener after the `deviceready` event fires.

### Windows Quirks

- If the device is not powered by battery, i.e. on desktop, the battery status will be reported with level always at 100% and isPlugged is always true.
- The value isPlugged is loosely converted from battery status reported by Windows (which are charging, discharging, and idle). Thus, when battery is fully changed (level at 100%) the isPlugged value remains true whether the device is currently plugged or not. It will change to false when the battery switches to discharging state.

### Example

    window.addEventListener("batterystatus", onBatteryStatus, false);

    function onBatteryStatus(info) {
        // Handle the online event
        console.log("Level: " + info.level + " isPlugged: " + info.isPlugged);
    }

## batterycritical

The event fires when the percentage of battery charge has reached the
critical battery threshold. The value is device-specific.

The `batterycritical` handler is passed an object that contains two
properties:

- __level__: The percentage of battery charge (0-100). _(Number)_

- __isPlugged__: A boolean that indicates whether the device is plugged in. _(Boolean)_

Applications typically should use `window.addEventListener` to attach
an event listener once the `deviceready` event fires.

### Example

    window.addEventListener("batterycritical", onBatteryCritical, false);

    function onBatteryCritical(info) {
        // Handle the battery critical event
        alert("Battery Level Critical " + info.level + "%\nRecharge Soon!");
    }

## batterylow

The event fires when the percentage of battery charge has reached the
low battery threshold, device-specific value.

The `batterylow` handler is passed an object that contains two
properties:

- __level__: The percentage of battery charge (0-100). _(Number)_

- __isPlugged__: A boolean that indicates whether the device is plugged in. _(Boolean)_

Applications typically should use `window.addEventListener` to
attach an event listener once the `deviceready` event fires.

### Example

    window.addEventListener("batterylow", onBatteryLow, false);

    function onBatteryLow(info) {
        // Handle the battery low event
        alert("Battery Level Low " + info.level + "%");
    }

