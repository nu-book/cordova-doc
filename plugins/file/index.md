# File


This plugin implements a File API allowing read/write access to files residing on the device.

This plugin is based on several specs, including : 
The HTML5 File API
[http://www.w3.org/TR/FileAPI/](http://www.w3.org/TR/FileAPI/)

The (now-defunct) Directories and System extensions
Latest: 
[http://www.w3.org/TR/2012/WD-file-system-api-20120417/](http://www.w3.org/TR/2012/WD-file-system-api-20120417/)
Although most of the plugin code was written when an earlier spec was current:
[http://www.w3.org/TR/2011/WD-file-system-api-20110419/](http://www.w3.org/TR/2011/WD-file-system-api-20110419/)

It also implements the FileWriter spec :
[http://dev.w3.org/2009/dap/file-system/file-writer.html](http://dev.w3.org/2009/dap/file-system/file-writer.html)

For usage, please refer to HTML5 Rocks' excellent [FileSystem article.](http://www.html5rocks.com/en/tutorials/file/filesystem/)

For an overview of other storage options, refer to Cordova's
[storage guide](http://cordova.apache.org/docs/en/edge/cordova_storage_storage.md.html).

This plugin defines global `cordova.file` object.

Although in the global scope, it is not available until after the `deviceready` event.

    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
        console.log(cordova.file);
    }

## Where to Store Files

URLs to important file-system directories are provided.
Each URL can be converted to a
`DirectoryEntry` using `window.resolveLocalFileSystemURL()`.

* `cordova.file.applicationStorageDirectory` - Root directory of the application's sandbox; on iOS this location is read-only (but specific subdirectories [like `/Documents`] are read-write). All data contained within is private to the app.

* `cordova.file.dataDirectory` - Persistent and private data storage within the application's sandbox using internal memory.

* `cordova.file.documentDataDirectory` - Per-document data storage. This is usually a sub-folder inside `dataDirectory`. Files inside this folder will be automatically removed when the document is removed from user's Library.

* `cordova.file.cacheDirectory` -  Directory for cached data files or any files that can re-create easily. The OS may delete these files when the device runs low on storage, nevertheless, you should not rely on the OS to delete files in here.

* `cordova.file.externalApplicationStorageDirectory` - Application space on external storage. (_Android only_)

* `cordova.file.externalDataDirectory` - Where to put app-specific data files on external storage. (_Android only_)

* `cordova.file.externalCacheDirectory` - Application cache on external storage. (_Android only_)

* `cordova.file.externalRootDirectory` - External storage (SD card) root. (_Android only_)

* `cordova.file.syncedDataDirectory` - Holds app-specific files that should be synced (e.g. to iCloud). (_iOS only_)

* `cordova.file.documentsDirectory` - Files private to the app, but that are meaningful to other application (e.g. Office files). (_iOS only_)

* `cordova.file.tempDirectory` - Temp directory that the OS can clear at will. Do not rely on the OS to clear this directory; your app should always remove files as applicable. (_iOS and Windows only_)

* `cordova.file.roamingDirectory` - Files in this folder is synced with user's profile. (_Windows only_)

### BlackBerry 10 File System Layout

| Device Path                                                  | `cordova.file.*`            | r/w? | persistent? | OS clears | private |
|:-------------------------------------------------------------|:----------------------------|:----:|:-----------:|:---------:|:-------:|
| `file:///accounts/1000/appdata/<app id>/`                    | applicationStorageDirectory | r    |     N/A     |     N/A   |   Yes   |
| &nbsp;&nbsp;&nbsp;`app/native`                               | applicationDirectory        | r    |     N/A     |     N/A   |   Yes   |
| &nbsp;&nbsp;&nbsp;`data/webviews/webfs/temporary/local__0`   | cacheDirectory              | r/w  |     No      |     Yes   |   Yes   |
| &nbsp;&nbsp;&nbsp;`data/webviews/webfs/persistent/local__0`  | dataDirectory               | r/w  |     Yes     |     No    |   Yes   |
| `file:///accounts/1000/removable/sdcard`                     | externalRemovableDirectory  | r/w  |     Yes     |     No    |   No    |
| `file:///accounts/1000/shared`                               | sharedDirectory             | r/w  |     Yes     |     No    |   No    |

*Note*: When application is deployed to work perimeter, all paths are relative to /accounts/1000-enterprise.

## Android Quirks

- If external storage isn't mounted, the `cordova.file.external*` properties are `null`.

## iOS Quirks

- `cordova.file.applicationStorageDirectory` is read-only; attempting to store files within the root directory will fail. Use one of the other `cordova.file.*` properties defined for iOS (only `applicationDirectory` and `applicationStorageDirectory` are read-only).
- `FileReader.readAsText(blob, encoding)`, the `encoding` parameter is not supported, and UTF-8 encoding is always in effect.

## List of Error Codes and Meanings
When an error is thrown, one of the following codes will be used. 

| Code | Constant                      |
|-----:|:------------------------------|
|    1 | `NOT_FOUND_ERR`               |
|    2 | `SECURITY_ERR`                |
|    3 | `ABORT_ERR`                   |
|    4 | `NOT_READABLE_ERR`            |
|    5 | `ENCODING_ERR`                |
|    6 | `NO_MODIFICATION_ALLOWED_ERR` |
|    7 | `INVALID_STATE_ERR`           |
|    8 | `SYNTAX_ERR`                  |
|    9 | `INVALID_MODIFICATION_ERR`    |
|   10 | `QUOTA_EXCEEDED_ERR`          |
|   11 | `TYPE_MISMATCH_ERR`           |
|   12 | `PATH_EXISTS_ERR`             |

