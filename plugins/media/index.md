# Media

This plugin provides the ability to record and play back audio files on a device.

__NOTE__:
* The current implementation does not adhere to a W3C specification for media capture, and is provided for convenience only.
* COTG provides its own media file playback which is much simpler: in your Javascript, just do `window.localtion.href = <url of media file>;` to play media urls. This interface provides no control to Javascript but works seamlessly on every platform we support. We recommend to use it instead of this plugin. This plugin is provided in case you want to control playback, play/pause/stop in the background directly from JavaScript.

This plugin defines a global `Media` Constructor.

Although in the global scope, it is not available until after the `deviceready` event.

    document.addEventListener("deviceready", onDeviceReady, false);
    function onDeviceReady() {
        console.log(Media);
    }


## Media

    var media = new Media(src, mediaSuccess, [mediaError], [mediaStatus]);

### Parameters

- __src__: A URI containing the audio content. _(DOMString)_

- __mediaSuccess__: (Optional) The callback that executes after a `Media` object has completed the current play, record, or stop action. _(Function)_

- __mediaError__: (Optional) The callback that executes if an error occurs. _(Function)_

- __mediaStatus__: (Optional) The callback that executes to indicate status changes. _(Function)_

### Constants

The following constants are reported as the only parameter to the
`mediaStatus` callback:

- `Media.MEDIA_NONE`     = 0;
- `Media.MEDIA_STARTING` = 1;
- `Media.MEDIA_RUNNING`  = 2;
- `Media.MEDIA_PAUSED`   = 3;
- `Media.MEDIA_STOPPED`  = 4;

### Methods

- `media.getCurrentPosition`: Returns the current position within an audio file.

- `media.getDuration`: Returns the duration of an audio file.

- `media.play`: Start or resume playing an audio file.

- `media.pause`: Pause playback of an audio file.

- `media.release`: Releases the underlying operating system's audio resources.

- `media.seekTo`: Moves the position within the audio file.

- `media.setVolume`: Set the volume for audio playback.

- `media.stop`: Stop playing an audio file.

### Additional ReadOnly Parameters

- __position__: The position within the audio playback, in seconds.
    - Not automatically updated during play; call `getCurrentPosition` to update.

- __duration__: The duration of the media, in seconds.


## media.getCurrentPosition

Returns the current position within an audio file.  Also updates the `Media` object's `position` parameter.

    media.getCurrentPosition(mediaSuccess, [mediaError]);

### Parameters

- __mediaSuccess__: The callback that is passed the current position in seconds.

- __mediaError__: (Optional) The callback to execute if an error occurs.

### Quick Example

    // Audio player
    //
    var my_media = new Media(src, onSuccess, onError);

    // Update media position every second
    var mediaTimer = setInterval(function () {
        // get media position
        my_media.getCurrentPosition(
            // success callback
            function (position) {
                if (position > -1) {
                    console.log((position) + " sec");
                }
            },
            // error callback
            function (e) {
                console.log("Error getting pos=" + e);
            }
        );
    }, 1000);


## media.getDuration

Returns the duration of an audio file in seconds. If the duration is unknown, it returns a value of -1.


    media.getDuration();

### Quick Example

    // Audio player
    //
    var my_media = new Media(src, onSuccess, onError);

    // Get duration
    var counter = 0;
    var timerDur = setInterval(function() {
        counter = counter + 100;
        if (counter > 2000) {
            clearInterval(timerDur);
        }
        var dur = my_media.getDuration();
        if (dur > 0) {
            clearInterval(timerDur);
            document.getElementById('audio_duration').innerHTML = (dur) + " sec";
        }
    }, 100);


## media.pause

Pauses playing an audio file.

    media.pause();


### Quick Example

    // Play audio
    //
    function playAudio(url) {
        // Play the audio file at url
        var my_media = new Media(url,
            // success callback
            function () { console.log("playAudio():Audio Success"); },
            // error callback
            function (err) { console.log("playAudio():Audio Error: " + err); }
        );

        // Play audio
        my_media.play();

        // Pause after 10 seconds
        setTimeout(function () {
            media.pause();
        }, 10000);
    }


## media.play

Starts or resumes playing an audio file.

    media.play();


### Quick Example

    // Play audio
    //
    function playAudio(url) {
        // Play the audio file at url
        var my_media = new Media(url,
            // success callback
            function () {
                console.log("playAudio():Audio Success");
            },
            // error callback
            function (err) {
                console.log("playAudio():Audio Error: " + err);
            }
        );
        // Play audio
        my_media.play();
    }


### iOS Quirks

- __numberOfLoops__: Pass this option to the `play` method to specify
  the number of times you want the media file to play, e.g.:

        var myMedia = new Media("http://audio.ibeat.org/content/p1rj1s/p1rj1s_-_rockGuitar.mp3")
        myMedia.play({ numberOfLoops: 2 })

- __playAudioWhenScreenIsLocked__: Pass in this option to the `play`
  method to specify whether you want to allow playback when the screen
  is locked.  If set to `true` (the default value), the state of the
  hardware mute button is ignored, e.g.:

        var myMedia = new Media("http://audio.ibeat.org/content/p1rj1s/p1rj1s_-_rockGuitar.mp3")
        myMedia.play({ playAudioWhenScreenIsLocked : false })

- __order of file search__: When only a file name or simple path is
  provided, iOS searches in the `www` directory for the file, then in
  the application's `documents/tmp` directory:

        var myMedia = new Media("audio/beer.mp3")
        myMedia.play()  // first looks for file in www/audio/beer.mp3 then in <application>/documents/tmp/audio/beer.mp3

## media.release

Releases the underlying operating system's audio resources.
This is particularly important for Android, since there are a finite amount of
OpenCore instances for media playback. Applications should call the `release`
function for any `Media` resource that is no longer needed.

    media.release();


### Quick Example

    // Audio player
    //
    var my_media = new Media(src, onSuccess, onError);

    my_media.play();
    my_media.stop();
    my_media.release();


## media.seekTo

Sets the current position within an audio file.

    media.seekTo(milliseconds);

### Parameters

- __milliseconds__: The position to set the playback position within the audio, in milliseconds.


### Quick Example

    // Audio player
    //
    var my_media = new Media(src, onSuccess, onError);
        my_media.play();
    // SeekTo to 10 seconds after 5 seconds
    setTimeout(function() {
        my_media.seekTo(10000);
    }, 5000);



## media.setVolume

Set the volume for an audio file.

    media.setVolume(volume);

### Parameters

- __volume__: The volume to set for playback.  The value must be within the range of 0.0 to 1.0.

### Quick Example

    // Play audio
    //
    function playAudio(url) {
        // Play the audio file at url
        var my_media = new Media(url,
            // success callback
            function() {
                console.log("playAudio():Audio Success");
            },
            // error callback
            function(err) {
                console.log("playAudio():Audio Error: "+err);
        });

        // Play audio
        my_media.play();

        // Mute volume after 2 seconds
        setTimeout(function() {
            my_media.setVolume('0.0');
        }, 2000);

        // Set volume to 1.0 after 5 seconds
        setTimeout(function() {
            my_media.setVolume('1.0');
        }, 5000);
    }


## media.stop

Stops playing an audio file.

    media.stop();

### Quick Example

    // Play audio
    //
    function playAudio(url) {
        // Play the audio file at url
        var my_media = new Media(url,
            // success callback
            function() {
                console.log("playAudio():Audio Success");
            },
            // error callback
            function(err) {
                console.log("playAudio():Audio Error: "+err);
            }
        );

        // Play audio
        my_media.play();

        // Pause after 10 seconds
        setTimeout(function() {
            my_media.stop();
        }, 10000);
    }


## MediaError

A `MediaError` object is returned to the `mediaError` callback
function when an error occurs.

### Properties

- __code__: One of the predefined error codes listed below.

- __message__: An error message describing the details of the error.

### Constants

- `MediaError.MEDIA_ERR_ABORTED`        = 1
- `MediaError.MEDIA_ERR_NETWORK`        = 2
- `MediaError.MEDIA_ERR_DECODE`         = 3
- `MediaError.MEDIA_ERR_NONE_SUPPORTED` = 4
