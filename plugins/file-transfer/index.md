# File Transfer

This plugin allows you to upload and download files.

This plugin defines global `FileTransfer`, `FileUploadOptions` Constructors.

Although in the global scope, they are not available until after the `deviceready` event.

    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
        console.log(FileTransfer);
    }

# FileTransfer

The `FileTransfer` object provides a way to upload files using an HTTP
multi-part POST request, and to download files as well.

## Properties

- __onprogress__: Called with a `ProgressEvent` whenever a new chunk of data is transferred. _(Function)_

## Methods

- __upload__: sends a file to a server.

- __download__: downloads a file from server.

- __abort__: Aborts an in-progress transfer.


## upload

__Syntax__:

`upload(fileUrl, serverUrl, successCallback, errorCallback, options)`

__Parameters__:

- __fileURL__: Filesystem URL representing the file on the device. For backwards compatibility, this can also be the full path of the file on the device. (See [Backwards Compatibility Notes] below)

- __server__: URL of the server to receive the file, as encoded by `encodeURI()`.

- __successCallback__: A callback that is passed a `FileUploadResult` object. _(Function)_

- __errorCallback__: A callback that executes if an error occurs retrieving the `FileUploadResult`. Invoked with a `FileTransferError` object. _(Function)_

- __options__: Optional parameters _(Object)_. Valid keys:
  - __fileKey__: The name of the form element.  Defaults to `file`. (DOMString)
  - __fileName__: The file name to use when saving the file on the server.  Defaults to `image.jpg`. (DOMString)
  - __httpMethod__: The HTTP method to use - either `PUT` or `POST`. Defaults to `POST`. (DOMString)
  - __mimeType__: The mime type of the data to upload.  Defaults to `image/jpeg`. (DOMString)
  - __params__: A set of optional key/value pairs to pass in the HTTP request. (Object)
  - __chunkedMode__: Whether to upload the data in chunked streaming mode. Defaults to `true`. (Boolean)
  - __headers__: A map of header name/header values. Use an array to specify more than one value. (Object)
  
- __trustAllHosts__: Optional parameter, defaults to `false`. If set to `true`, it accepts all security certificates. This is useful since Android rejects self-signed security certificates. Not recommended for production use. Supported on Android and iOS. _(boolean)_

### Example

    // !! Assumes variable fileURL contains a valid URL to a text file on the device,
    //    for example, cdvfile://localhost/persistent/path/to/file.txt

    var win = function (r) {
        console.log("Code = " + r.responseCode);
        console.log("Response = " + r.response);
        console.log("Sent = " + r.bytesSent);
    }

    var fail = function (error) {
        alert("An error has occurred: Code = " + error.code);
        console.log("upload error source " + error.source);
        console.log("upload error target " + error.target);
    }

    var options = new FileUploadOptions();
    options.fileKey = "file";
    options.fileName = fileURL.substr(fileURL.lastIndexOf('/') + 1);
    options.mimeType = "text/plain";

    var params = {};
    params.value1 = "test";
    params.value2 = "param";

    options.params = params;

    var ft = new FileTransfer();
    ft.upload(fileURL, encodeURI("http://some.server.com/upload.php"), win, fail, options);

### Example with Upload Headers and Progress Events (Android and iOS only)

    function win(r) {
        console.log("Code = " + r.responseCode);
        console.log("Response = " + r.response);
        console.log("Sent = " + r.bytesSent);
    }

    function fail(error) {
        alert("An error has occurred: Code = " + error.code);
        console.log("upload error source " + error.source);
        console.log("upload error target " + error.target);
    }

    var uri = encodeURI("http://some.server.com/upload.php");

    var options = new FileUploadOptions();
    options.fileKey="file";
    options.fileName=fileURL.substr(fileURL.lastIndexOf('/')+1);
    options.mimeType="text/plain";

    var headers={'headerParam':'headerValue'};

    options.headers = headers;

    var ft = new FileTransfer();
    ft.onprogress = function(progressEvent) {
        if (progressEvent.lengthComputable) {
          loadingStatus.setPercentage(progressEvent.loaded / progressEvent.total);
        } else {
          loadingStatus.increment();
        }
    };
    ft.upload(fileURL, uri, win, fail, options);

## FileUploadResult

A `FileUploadResult` object is passed to the success callback of the `FileTransfer` object's `upload()` method.

### Properties

- __bytesSent__: The number of bytes sent to the server as part of the upload. (long)

- __responseCode__: The HTTP response code returned by the server. (long)

- __response__: The HTTP response returned by the server. (DOMString)

- __headers__: The HTTP response headers by the server. (Object)
  - Currently supported on iOS only.

### iOS Quirks

- Does not support `responseCode` or `bytesSent`.


## download

__Syntax__:

download(source, target, successCallback, errorCallback, trustAllHosts, options)

__Parameters__:

- __source__: URL of the server to download the file, as encoded by `encodeURI()`.

- __target__: Filesystem url representing the file on the device. For backwards compatibility, this can also be the full path of the file on the device. (See [Backwards Compatibility Notes] below)

- __successCallback__: A callback that is passed  a `FileEntry` object. _(Function)_

- __errorCallback__: A callback that executes if an error occurs when retrieving the `FileEntry`. Invoked with a `FileTransferError` object. _(Function)_

- __trustAllHosts__: Optional parameter, defaults to `false`. If set to `true`, it accepts all security certificates. This is useful because Android rejects self-signed security certificates. Not recommended for production use. Supported on Android and iOS. _(boolean)_

- __options__: Optional parameters, currently only supports headers (such as Authorization (Basic Authentication), etc).

### Example

    // !! Assumes variable fileURL contains a valid URL to a path on the device,
    //    for example, cdvfile://localhost/persistent/path/to/downloads/

    var fileTransfer = new FileTransfer();
    var uri = encodeURI("http://some.server.com/download.php");

    fileTransfer.download(
        uri,
        fileURL,
        function(entry) {
            console.log("download complete: " + entry.toURL());
        },
        function(error) {
            console.log("download error source " + error.source);
            console.log("download error target " + error.target);
            console.log("upload error code" + error.code);
        },
        false,
        {
            headers: {
                "Authorization": "Basic dGVzdHVzZXJuYW1lOnRlc3RwYXNzd29yZA=="
            }
        }
    );

## abort

Aborts an in-progress transfer. The onerror callback is passed a FileTransferError object which has an error code of FileTransferError.ABORT_ERR.

### Example

    // !! Assumes variable fileURL contains a valid URL to a text file on the device,
    //    for example, cdvfile://localhost/persistent/path/to/file.txt

    var win = function(r) {
        console.log("Should not be called.");
    }

    var fail = function(error) {
        // error.code == FileTransferError.ABORT_ERR
        alert("An error has occurred: Code = " + error.code);
        console.log("upload error source " + error.source);
        console.log("upload error target " + error.target);
    }

    var options = new FileUploadOptions();
    options.fileKey="file";
    options.fileName="myphoto.jpg";
    options.mimeType="image/jpeg";

    var ft = new FileTransfer();
    ft.upload(fileURL, encodeURI("http://some.server.com/upload.php"), win, fail, options);
    ft.abort();


## FileTransferError

A `FileTransferError` object is passed to an error callback when an error occurs.

### Properties

- __code__: One of the predefined error codes listed below. (Number)

- __source__: URL to the source. (String)

- __target__: URL to the target. (String)

- __http_status__: HTTP status code.  This attribute is only available when a response code is received from the HTTP connection. (Number)

- __body__ Response body. This attribute is only available when a response is received from the HTTP connection. (String)

- __exception__: Either e.getMessage or e.toString (String)

### Constants

- 1 = `FileTransferError.FILE_NOT_FOUND_ERR`
- 2 = `FileTransferError.INVALID_URL_ERR`
- 3 = `FileTransferError.CONNECTION_ERR`
- 4 = `FileTransferError.ABORT_ERR`
- 5 = `FileTransferError.NOT_MODIFIED_ERR`
