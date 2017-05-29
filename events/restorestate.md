# restorestate

The event fires when the document is re-opened.

    window.addEventListener("restorestate", yourCallbackFunction, false);
    
Note that this event is attached to `window` not `document` as lifecycle events.

## Details

The `restorestate` event fires to give an opportunity to restore data that are not persisted by automatic mechanism. The `event` parameter is a `CustomEvent`, where `detail` is an object containing object `state`, an associative array where you can read your custom data what has been written in `savestate`.

This event is garanted to fires at most once.

You must attach your callbacks **as soon as possible**, i.e. directly at global scope, since, by contrast to `deviceready` for which callbacks will be called immediately if you attach them after the event is fired, `restorestate` will not call your callbacks if you miss the shot.

Depending on the device, `restorestate` may fire before as well as after the `deviceready` event. Thus, in your callback, you must check for `deviceready` before use Cordova's features. 

Beware your data is converted to JSON in `savestate` and restored from JSON here, thus objects such as Date will be converted to strings when you get them back in your callback.

Note that form's fields that have `name` attribute will save and restore their values automatically except for controls that are added dynamically by Javascript. That means that, in your callback function, you only need to take care of these dynamic elements.

Your javascript must use `window.addEventListener` to attach an event listener.

## Quick Example

    window.addEventListener("restorestate", onRestoreState, false);

    function onRestoreState(event) {
        var state = event.detail.state;
        var value2 = state['field2'];
        if (value2) {
            var input = $('<input id="field2"/>');
            input.val(value2);
            $('#form1').append(input);
        }
    }
    
## Full Example

    <!DOCTYPE html>
    <html>
      <head>
		<script type="text/javascript" src="http://code.jquery.com/jquery-2.1.4.min.js"></script>
        <script type="text/javascript">

        window.addEventListener("savestate", onSaveState, false);
        window.addEventListener("restorestate", onRestoreState, false);
        
        function onSaveState(event) {
        	var state = event.detail.state;
        	// field1 is static, and handled automatically;
            state['field2'] = $('#field2').val();
    	}

        function onRestoreState(event) {
        	var state = event.detail.state;
        	var value2 = state['field2'];
            if (value2) {
            	var input = $('<input id="field2"/>');
                input.val(value2);
            	$('#form1').append(input);
            }
    	}
        </script>
      </head>
      <body>
      	<form id="form1">
      	  <input type="text" name="field1" id="field1" />
      	</form>
      </body>
    </html>

