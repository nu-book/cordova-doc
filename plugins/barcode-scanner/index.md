# Barcode Scanner

This plugin provides interface to start a barcode scanning using device's buit-in camera.

This plugin creates the object "navigator.barcodeScanner" with single method `scan(success, fail)`, where:

* `success` is a callback which will be called when the scan succeed or cancelled by user. This callback will receive one argument, which is an object with the following properties:
  *	`data` (string) is the text representation of the barcode data;
  *	`type` (string) is the type of barcode detected; 
  * `cancelled` (boolean) whether or not the user cancelled the scan.
* `fail` is a callback which will be called if there is error occurs. This callback will receive the error message as string.

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
