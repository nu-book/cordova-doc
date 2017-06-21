# COTG Host

This plugin provides access some properties and services related to the context of the running app that hosts the HTML renderer.

This plugin defines a global `navigator.cotgHost` object.

Although the object is in the global scope, features provided by this plugin are not available until after the `deviceready` event.

    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
        console.log(navigator.cotgHost);
    }

## Properties

- __navigator.cotgHost.loginIdentifier__: Email address of current user
- __navigator.cotgHost.documentIdentifier__: Current document ID
- __navigator.cotgHost.repositoryIdentifier__: Active reposity

### navigator.cotgHost.loginIdentifier
This property of the "navigator.cotgHost" object contains the email address of the user connected to the current session in the mobile app.

### navigator.cotgHost.documentIdentifier
This property of the "navigator.cotgHost" object contains the identifier of the document. For template instances, it is a UUID created by the app, otherwise it is the DocumentID returned from the Document Deposit service.

### navigator.cotgHost.repositoryIdentifier
[_From 10.3 only_]

This property of the "navigator.cotgHost" object contains the identifier of the current active repository. Note that this may be different from the repository where the document comes from.


## Methods

- __navigator.cotgHost.hasDocument__: Check download state of a document
- __navigator.cotgHost.downloadDocument__: Trigger document download
- __navigator.cotgHost.openDocument__: Open a document
- __navigator.cotgHost.pickFile__: Allow user to pick a file from her device
- __navigator.cotgHost.parseFileInfo__: restore an object FileInfo from string

### navigator.cotgHost.hasDocument
[_From 10.5 only_]

Check whether a document is downloaded (visible in Library view).

#### Syntax
`navigator.cotgHost.hasDocument(docId, successCallback, errorCallback)`

#### Parameters
* __docId__: The DocumentID returned by the Deposit service.
* __successCallback__: A callback `function(downloaded)` where `downloaded` is a boolean which indicates the installation state of the document.
* __errorCallback__:	A callback `function(error)` which is called in case of exception. `error` is a string describing error.


### navigator.cotgHost.downloadDocument
[_From 10.5 only_]

Download the document with given id.

#### Syntax
`navigator.cotgHost.downloadDocument(docId, successCallback, errorCallback)`

#### Parameters
* __docId__: The DocumentID returned by the Deposit service.
* __successCallback__: A callback `function()` which is called if the given document is already downloaded or has been downloaded successfully.
* __errorCallback__: A callback `function(error)` which is called in case the download has failed. `error` is a string describing error.

### navigator.cotgHost.openDocument
[_From 10.5 only_]

Open the document with given id.

#### Syntax
`navigator.cotgHost.openDocument(docId, successCallback, errorCallback)`

#### Parameters
* __docId__: The DocumentID returned by the Deposit service.
* __successCallback__: A callback `function()` which is called just before the document is opened.
* __errorCallback__: A callback `function(error)` which is called in case the download has failed. `error` is a string describing error.

#### Remarks
If the document is not yet download, this method will download it and then open it.


### navigator.cotgHost.pickFile
[_From 10.5 only_]

Show an interface allow user to pick a file that can be submitted with the form as attachment.

Other than compatibility issues, there are technical issues that prevent a `<input type="file">` from working properly in offline context of COTG. This method is provided as an alternative solution. It works a little bit different but it will the same result at the end.

#### Syntax
`navigator.cotgHost.pickFile(successCallback, errorCallback, options)`

#### Parameters
* __successCallback__: A callback which is called after user has picked a file or cancel the process. This callback will receive an array of `FileInfo` objects. When user cancels the process, this array is empty.
* __errorCallback__: A callback `function(error)` which is called in case of unexpected error. `error` will contain error message.
* __options__: is an object which can contain following optional parameters:
  * __allowMultiple__: Allow user to pick more than one file (Android and Windows only).

#### Full Example
    <html>
    <head>
    <script>

    // file must be type of FileInfo
    function updateChosenFile(file) {
    	document.getElementById('uploadFileName').innerHTML = file ? file.name : '';
    	document.getElementById('uploadFile').value = file || ''; // implicitly convert to string
    }

    window.addEventListener('restorestate', function(event) {
    	var uploadFile = document.getElementById('uploadFile').value;
        if (uploadFile) {
        	document.addEventListener('deviceready', function() {
                var fileName = navigator.cotgHost.parseFileInfo(uploadFile).name;
            	document.getElementById('uploadFileName').innerHTML = fileName;
            }, false);           
        }
    }, false);

    function chooseFile() {
        navigator.cotgHost.pickFile(
        	// success, files is an array of FileInfo, files.length == 0 if user cancels
        	function(files) {
        		updateChosenFile(files[0]);
        	},
        	// error, exception only
        	alert,
        	// options
        	{
        		allowMultiple: false,  // Allow multiple selections, default is false
        	}
        );
    }
    </script>
    </head>
    <body>
      <form action="..." method="post" enctype="multipart/form-data" >
        <fieldset>
          <legend>File Upload (using File Chooser plugin)</legend>
      	  <label>Choose a file and press "Send"</label><br/><br/>
          File to send:
          <span id="uploadFileName"></span>
          <input type="button" value="Choose File" onclick="chooseFile()"/><br/><br/>
          <input type="hidden" name="uploadFile" id="uploadFile" />
          <input type="submit" value="Send" />
        </fieldset>
      </form>
    </body>
	</html>
    
### navigator.cotgHost.parseFileInfo
[_From 10.5 only_]

Parse `FileInfo` object from string.

#### Syntax
`navigator.cotgHost.parseFileInfo(str)`

#### Parameters
* __str__: A string which was the result of `FileInfo.toString()`.
#### Result
A FileInfo object if the parsing was successful, `undefined` otherwise.


## Objects
### FileInfo

Contains following properties

* __name__: File name
* __type__: File mimetype, e.g. `image/jpeg`
* __size__: File size in bytes
* __url__: The URL of the file.

Object instances of FileInfo implement `toString()` method whose result must be submitted together with the form. When sending data to server, COTG will interpret these strings and will send the files as attachments.
