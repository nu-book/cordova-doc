# DatePicker


## Usage

```js
var options = {
    date: new Date(),
    mode: 'date'
};

function onSuccess(date) {
    alert('Selected date: ' + date);
}

function onError(error) { // Android only
    alert('Error: ' + error);
}

datePicker.show(options, onSuccess, onError);
```

## Options

### mode - iOS, Android, Windows
The mode of the date picker.

Type: String

Values: `date` | `time` | `datetime` (iOS, Windows only)

Default: `date`

### date - iOS, Android, Windows
Selected date.

Type: String

Default: `new Date()`

### minDate - iOS, Android, Windows
Minimum date.

Type: Date | Number (unix timestamp)

Default: `undefined`

On Android, only the date is enforced (time is not).

### maxDate - iOS, Android, Windows
Maximum date.

Type: Date | Number (unix timestamp)

Default: `undefined`
