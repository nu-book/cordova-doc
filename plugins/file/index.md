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

As of v1.2.0, URLs to important file-system directories are provided.
Each URL is in the form _file:///path/to/spot/_, and can be converted to a
`DirectoryEntry` using `window.resolveLocalFileSystemURL()`.

* `cordova.file.applicationDirectory` - Read-only directory where the application 
  is installed. (_iOS_, _Android_, _BlackBerry 10_)

* `cordova.file.applicationStorageDirectory` - Root directory of the application's 
  sandbox; on iOS this location is read-only (but specific subdirectories [like 
  `/Documents`] are read-write). All data contained within is private to the app. (
  _iOS_, _Android_, _BlackBerry 10_)

* `cordova.file.dataDirectory` - Persistent and private data storage within the 
  application's sandbox using internal memory (on Android, if you need to use 
  external memory, use `.externalDataDirectory`). On iOS, this directory is not 
  synced with iCloud (use `.syncedDataDirectory`). (_iOS_, _Android_, _BlackBerry 10_)

* `cordova.file.cacheDirectory` -  Directory for cached data files or any files 
  that your app can re-create easily. The OS may delete these files when the device 
  runs low on storage, nevertheless, apps should not rely on the OS to delete files 
  in here. (_iOS_, _Android_, _BlackBerry 10_)

* `cordova.file.externalApplicationStorageDirectory` - Application space on 
  external storage. (_Android_)

* `cordova.file.externalDataDirectory` - Where to put app-specific data files on 
  external storage. (_Android_)

* `cordova.file.externalCacheDirectory` - Application cache on external storage. 
  (_Android_)

* `cordova.file.externalRootDirectory` - External storage (SD card) root. (_Android_, _BlackBerry 10_)

* `cordova.file.tempDirectory` - Temp directory that the OS can clear at will. Do not 
  rely on the OS to clear this directory; your app should always remove files as 
  applicable. (_iOS_)

* `cordova.file.syncedDataDirectory` - Holds app-specific files that should be synced 
  (e.g. to iCloud). (_iOS_)

* `cordova.file.documentsDirectory` - Files private to the app, but that are meaningful 
  to other application (e.g. Office files). (_iOS_)


## File System Layouts

Although technically an implementation detail, it can be very useful to know how 
the `cordova.file.*` properties map to physical paths on a real device.

### iOS File System Layout

| Device Path                                    | `cordova.file.*`            | `iosExtraFileSystems` | r/w? | persistent? | OS clears | sync | private |
|:-----------------------------------------------|:----------------------------|:----------------------|:----:|:-----------:|:---------:|:----:|:-------:|
| `/var/mobile/Applications/<UUID>/`             | applicationStorageDirectory | -                     | r    |     N/A     |     N/A   | N/A  |   Yes   |
| &nbsp;&nbsp;&nbsp;`appname.app/`               | applicationDirectory        | bundle                | r    |     N/A     |     N/A   | N/A  |   Yes   |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`www/`     | -                           | -                     | r    |     N/A     |     N/A   | N/A  |   Yes   |
| &nbsp;&nbsp;&nbsp;`Documents/`                 | documentsDirectory          | documents             | r/w  |     Yes     |     No    | Yes  |   Yes   |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`NoCloud/` | -                           | documents-nosync      | r/w  |     Yes     |     No    | No   |   Yes   |
| &nbsp;&nbsp;&nbsp;`Library`                    | -                           | library               | r/w  |     Yes     |     No    | Yes? |   Yes   |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`NoCloud/` | dataDirectory               | library-nosync        | r/w  |     Yes     |     No    | No   |   Yes   |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`Cloud/`   | syncedDataDirectory         | -                     | r/w  |     Yes     |     No    | Yes  |   Yes   |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`Caches/`  | cacheDirectory              | cache                 | r/w  |     Yes*    |  Yes\*\*\*| No   |   Yes   |
| &nbsp;&nbsp;&nbsp;`tmp/`                       | tempDirectory               | -                     | r/w  |     No\*\*  |  Yes\*\*\*| No   |   Yes   |


  \* Files persist across app restarts and upgrades, but this directory can 
     be cleared whenever the OS desires. Your app should be able to recreate any 
     content that might be deleted.

\*\* Files may persist across app restarts, but do not rely on this behavior. Files 
     are not guaranteed to persist across updates. Your app should remove files from 
     this directory when it is applicable, as the OS does not guarantee when (or even 
     if) these files are removed.

\*\*\* The OS may clear the contents of this directory whenever it feels it is 
     necessary, but do not rely on this. You should clear this directory as 
     appropriate for your application.

### Android File System Layout

| Device Path                                     | `cordova.file.*`            | `AndroidExtraFileSystems` | r/w? | persistent? | OS clears | private |
|:------------------------------------------------|:----------------------------|:--------------------------|:----:|:-----------:|:---------:|:-------:|
| `file:///android_asset/`                        | applicationDirectory        |                           | r    |     N/A     |     N/A   |   Yes   |
| `/data/data/<app-id>/`                          | applicationStorageDirectory | -                         | r/w  |     N/A     |     N/A   |   Yes   |
| &nbsp;&nbsp;&nbsp;`cache`                       | cacheDirectory              | cache                     | r/w  |     Yes     |     Yes\* |   Yes   |
| &nbsp;&nbsp;&nbsp;`files`                       | dataDirectory               | files                     | r/w  |     Yes     |     No    |   Yes   |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`Documents` |                             | documents                 | r/w  |     Yes     |     No    |   Yes   |
| `<sdcard>/`                                     | externalRootDirectory       | sdcard                    | r/w  |     Yes     |     No    |   No    |
| &nbsp;&nbsp;&nbsp;`Android/data/<app-id>/`      | externalApplicationStorageDirectory | -                 | r/w  |     Yes     |     No    |   No    |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`cache`     | externalCacheDirectry       | cache-external            | r/w  |     Yes     |     No\*\*|   No    |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`files`     | externalDataDirectory       | files-external            | r/w  |     Yes     |     No    |   No    |

\* The OS may periodically clear this directory, but do not rely on this behavior. Clear 
   the contents of this directory as appropriate for your application. Should a user 
   purge the cache manually, the contents of this directory are removed.

\*\* The OS does not clear this directory automatically; you are responsible for managing 
     the contents yourself. Should the user purge the cache manually, the contents of the 
     directory are removed.

**Note**: If external storage can't be mounted, the `cordova.file.external*` 
properties are `null`.

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

### Android Persistent storage location

There are multiple valid locations to store persistent files on an Android
device. See [this page](http://developer.android.com/guide/topics/data/data-storage.html)
for an extensive discussion of the various possibilities.

Previous versions of the plugin would choose the location of the temporary and
persistent files on startup, based on whether the device claimed that the SD
Card (or equivalent storage partition) was mounted. If the SD Card was mounted,
or if a large internal storage partition was available (such as on Nexus
devices,) then the persistent files would be stored in the root of that space.
This meant that all Cordova apps could see all of the files available on the
card.

If the SD card was not available, then previous versions would store data under
`/data/data/<packageId>`, which isolates apps from each other, but may still
cause data to be shared between users.

It is now possible to choose whether to store files in the internal file
storage location, or using the previous logic, with a preference in your
application's `config.xml` file. To do this, add one of these two lines to
`config.xml`:

    <preference name="AndroidPersistentFileLocation" value="Internal" />

    <preference name="AndroidPersistentFileLocation" value="Compatibility" />

Without this line, the File plugin will use `Compatibility` as the default. If
a preference tag is present, and is not one of these values, the application
will not start.

If your application has previously been shipped to users, using an older (pre-
1.0) version of this plugin, and has stored files in the persistent filesystem,
then you should set the preference to `Compatibility`. Switching the location to
"Internal" would mean that existing users who upgrade their application may be
unable to access their previously-stored files, depending on their device.

If your application is new, or has never previously stored files in the
persistent filesystem, then the `Internal` setting is generally recommended.


## iOS Quirks

- `cordova.file.applicationStorageDirectory` is read-only; attempting to store 
  files within the root directory will fail. Use one of the other `cordova.file.*`
  properties defined for iOS (only `applicationDirectory` and `applicationStorageDirectory` are
  read-only).
- `FileReader.readAsText(blob, encoding)`
  - The `encoding` parameter is not supported, and UTF-8 encoding is always in effect.

### iOS Persistent storage location

There are two valid locations to store persistent files on an iOS device: the
Documents directory and the Library directory. Previous versions of the plugin
only ever stored persistent files in the Documents directory. This had the
side-effect of making all of an application's files visible in iTunes, which
was often unintended, especially for applications which handle lots of small
files, rather than producing complete documents for export, which is the
intended purpose of the directory.

It is now possible to choose whether to store files in the documents or library
directory, with a preference in your application's `config.xml` file. To do this,
add one of these two lines to `config.xml`:

    <preference name="iosPersistentFileLocation" value="Library" />

    <preference name="iosPersistentFileLocation" value="Compatibility" />

Without this line, the File plugin will use `Compatibility` as the default. If
a preference tag is present, and is not one of these values, the application
will not start.

If your application has previously been shipped to users, using an older (pre-
1.0) version of this plugin, and has stored files in the persistent filesystem,
then you should set the preference to `Compatibility`. Switching the location to
`Library` would mean that existing users who upgrade their application would be
unable to access their previously-stored files.

If your application is new, or has never previously stored files in the
persistent filesystem, then the `Library` setting is generally recommended.


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

## Configuring the Plugin (Optional)

The set of available filesystems can be configured per-platform. Both iOS and
Android recognize a <preference> tag in `config.xml` which names the
filesystems to be installed. By default, all file-system roots are enabled.

    <preference name="iosExtraFilesystems" value="library,library-nosync,documents,documents-nosync,cache,bundle,root" />
    <preference name="AndroidExtraFilesystems" value="files,files-external,documents,sdcard,cache,cache-external,root" />

### Android

* `files`: The application's internal file storage directory
* `files-external`: The application's external file storage directory
* `sdcard`: The global external file storage directory (this is the root of the SD card, if one is installed). You must have the `android.permission.WRITE_EXTERNAL_STORAGE` permission to use this.
* `cache`: The application's internal cache directory
* `cache-external`: The application's external cache directory
* `root`: The entire device filesystem

Android also supports a special filesystem named "documents", which represents a "/Documents/" subdirectory within the "files" filesystem.

### iOS

* `library`: The application's Library directory
* `documents`: The application's Documents directory
* `cache`: The application's Cache directory
* `bundle`: The application's bundle; the location of the app itself on disk (read-only)
* `root`: The entire device filesystem

By default, the library and documents directories can be synced to iCloud. You can also request two additional filesystems, `library-nosync` and `documents-nosync`, which represent a special non-synced directory within the `/Library` or `/Documents` filesystem.
