# COTG Host

This plugin provides access some properties related to the context of the running app that hosts the HTML renderer.

This plugin defines a global `navigator.cotgHost` object.

Although the object is in the global scope, features provided by this plugin are not available until after the `deviceready` event.

    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
        console.log(navigator.cotgHost);
    }

## Properties

- navigator.cotgHost.loginIdentifier
- navigator.cotgHost.documentIdentifier
- navigator.cotgHost.repositoryIdentifier

### navigator.cotgHost.loginIdentifier
This property of the "navigator.cotgHost" object contains the email address of the user connected to the current session in the mobile app.

### navigator.cotgHost.documentIdentifier
This property of the "navigator.cotgHost" object contains the identifier of the document. For template instances, it is a UUID created by the app, otherwise it is the DocumentID returned from the Document Deposit service.

### navigator.cotgHost.repositoryIdentifier
[_From 10.3 only_]

This property of the "navigator.cotgHost" object contains the identifier of the current active repository. Note that this may be different from the repository where the document comes from.


## Methods

- navigator.cotgHost.hasDocument
- navigator.cotgHost.downloadDocument
- navigator.cotgHost.openDocument

### navigator.cotgHost.hasDocument
[_From 10.5 only_]

Check whether a document is downloaded (visible in Library view).

#### Syntax
navigator.cotgHost.hasDocument(docId, successCallback, errorCallback)

#### Parameters
* __docId__: The DocumentID returned by the Deposit service.
* __successCallback__: A callback `function(downloaded)` where `downloaded` is a boolean which indicates the installation state of the document.
* __errorCallback__:	A callback `function(error)` which is called in case of exception. `error` is a string describing error.


### navigator.cotgHost.downloadDocument
[_From 10.5 only_]

Download the document with given id.

#### Syntax
navigator.cotgHost.downloadDocument(docId, successCallback, errorCallback)

#### Parameters
* __docId__: The DocumentID returned by the Deposit service.
* __successCallback__: A callback `function()` which is called if the given document is already downloaded or has been downloaded successfully.
* __errorCallback__: A callback `function(error)` which is called in case the download has failed. `error` is a string describing error.

### navigator.cotgHost.openDocument
[_From 10.5 only_]

Open the document with given id.

#### Syntax
navigator.cotgHost.openDocument(docId, successCallback, errorCallback)

#### Parameters
* __docId__: The DocumentID returned by the Deposit service.
* __successCallback__: A callback `function()` which is called just before the document is opened.
* __errorCallback__: A callback `function(error)` which is called in case the download has failed. `error` is a string describing error.

#### Remarks
If the document is not yet download, this method will download it and then open it.
