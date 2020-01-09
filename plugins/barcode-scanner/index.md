# Barcode Scanner

This plugin provides interface to start a barcode scanning using device's buit-in camera.

This plugin creates the object "navigator.barcodeScanner" with single method `scan(success, fail, options)`, where:

* `success` is a callback which will be called when the scan succeed or cancelled by user. This callback will receive one argument, which is an object with the following properties:
  *	`data` (string) is the text representation of the barcode data;
  *	`type` (string) is the type of barcode detected; 
  * `cancelled` (boolean) whether or not the user cancelled the scan.
* `fail` is a callback which will be called if there is error occurs. This callback will receive the error message as string.

### Options

- __cameraDirection__: (From version 10.8) Choose the camera to use (front- or back-facing). Defined in `Camera.Direction` _(Number)_

        Camera.Direction = {
            BACK: 0,
            FRONT: 1
        };
        
    Note that on Android, this option will work only if the orginal app from ZXing Team, [Barcode Scanner](https://play.google.com/store/apps/details?id=com.google.zxing.client.android) is installed. Both [Barcode Scanner+ (Plus)](https://play.google.com/store/apps/details?id=com.srowen.bs.android) and [Barcode Scanner+ Simple](https://play.google.com/store/apps/details?id=com.srowen.bs.android.simple) will allow to scan barecodes but neither of them
    supports this option.

    Note also that use front-facing camera to read barcodes may result in suboptimal experience as the front-facing camera is generally far less performant than the back-facing one. We have many reported cases where front-facing camera takes much longer time to focus, especially in low light condition.
    Focus is very important to scan quickly and correctly barcodes.

#### Example
    navigator.barcodeScanner.scan(
      function (result) {
          console.log("We got a barcode\n" +
                "Result: " + result.text + "\n" +
                "Format: " + result.format + "\n" +
                "Cancelled: " + result.cancelled);
      }, 
      function (error) {
          console.log("Scanning failed: " + error);
      }
    );

## Supported barcode types

#### On iOS (depend on actual iOS version, may be subject to changes)

* UPC_E
* CODE_39
* EAN_13
* EAN_8
* CODE_93
* CODE_128
* PDF_417
* QR_CODE
* AZTEC
* I2OF5
* ITF
* DATA_MATRIX

#### On Android and Windows:

* QR_CODE
* DATA_MATRIX
* AZTEC
* UPC_E
* UPC_A
* EAN_8
* EAN_13
* CODE_128
* CODE_39
* CODE_93
* CODABAR
* ITF
* RSS14
* PDF417
* RSS_EXPANDED


#### Android Quirks

- The `scan` method his will launch Barcode Scanner app from ZXing Team if it's already installed. Otherwise it will ask user to install it.
