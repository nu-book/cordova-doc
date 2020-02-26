# NFC

This plugin provides a way to write/read NDEF messages to/from NFC tags. On Android, this can also be used to exchange NDEF messages with other peer devices.

The API provided by this plugin follows closely the upcoming [Web NFC](https://w3c.github.io/web-nfc/) proposition. It adds `NDEFRecord`, `NDEFMessage`, `NDEFReadingEvent`, `NDEFReader`, and `NDEFWriter` class definitions directly into window's scope. The entry points are `NDEFReader` and `NDEFWriter` which are [documented here](https://w3c.github.io/web-nfc/#the-ndefreader-and-ndefwriter-objects). The  [examples section](https://w3c.github.io/web-nfc/#examples) are also very useful to have an overview.

Note that classes mentioned above may be replaced with native implementation in a future version of COTG if WebView supports them. Thus, cares should be taken when handling these objects, particularly when passing to `JSON.stringify` since this method will not work with native objects.

### Demo
A fully functional demo is available [here](https://nu-book.github.io/test-form-generator/mod-nfc.html)

