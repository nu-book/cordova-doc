# savestate

The event fires when user is about to leave the form (by closing it or leaving app) or the device is about to go to sleep state.

    window.addEventListener("savestate", yourCallbackFunction, false);
    
Note that this event is attached to `window` not `document` as lifecycle events.

## Details

The `savestate` event fires when it's time to save form state. The `event` parameter is a `CustomEvent`, where `detail` is an object containing object `state`, an associative array where you can write your custom data.

In common usage patterns, this event fires at least once. It may fires multiple times.

There are similarities between `savestate` and `pause` ([see pause](pause.md)), but their behaviors are differents. In contrast to the later, you can attach the former whenever and don't need to wait for `deviceready`. In cases where both events fires, `savestate` will always fires *before*. But `savestate` may just fires alone without followed by `pause`.

Note that form's fields that have `name` attribute will save and restore their values automatically except for controls that are added dynamically by Javascript. That means that, in your callback function, you only need to take care of these dynamic elements.

In your callback function, you should not perform asynchronous operations (e.g. send updated data to server using XMLHttpRequest). This is because after your callback returns, depends on situation, javascript code may continue to run or may be stopped right away. Thus your async operations may have chance to complete in some cases but not in other cases, which is source of hard-to-track bugs.

Your javascript must use `window.addEventListener` to attach an event listener.

## Quick Example

    window.addEventListener("savestate", onSaveState, false);

    function onSaveState(event) {
        var state = event.detail.state;
        // field1 is static, and handled automatically;
        state['field2'] = $('#field2').val();
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

