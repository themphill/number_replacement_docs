# SourceTrak Number Replacement

## Summary

When SourceTrak loads, it will replace all the numbers that match the provided number replacement options in the page title and the text of the page body, including "tel" links* and title attributes. Number replacement only happens once, so any numbers added to the page afterwards will not be replaced. Numbers present in images, JavaScript, or in HTML (except as noted above) will also not be replaced.

For use cases not covered by the default behavior, the SourceTrak number is made available in the SourceTrak JavaScript code running on the page. This allows customers to write their own JavaScript code that can access the number for custom use.

*: In HTML, a "tel" link is of the format `<a href="tel:5555555555"></a>`. The number `5555555555` would be replaced.

## Technical Specification

### Number Replacement

SourceTrak number replacement begins with the snippet that has been added to a page. Unless you are using a tag manager, it is recommended to place the snippet before the closing `</body>` tag:

```html
<html>
	<head>
		<title></title>
	</head>

	<body>
        <script type="text/javascript">
            // your Ifbyphone public key is automatically included
            // in the snippet shown in the SourceTrak application
            var _stk = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0";

            (function(){
                var a=document, b=a.createElement("script"); b.type="text/javascript";
                b.async=!0; b.src=('https:'==document.location.protocol ? 'https://' :
                'http://') + 'd31y97ze264gaa.cloudfront.net/assets/st/js/st.js';
                a=a.getElementsByTagName("script")[0]; a.parentNode.insertBefore(b,a);
            })();
        </script>
    </body>
</html>
```

When the page loads, the snippet will fetch the SourceTrak JavaScript ("the script") from either the Ifbyphone servers or the browser’s cache. The script will then begin number replacement based on the phone number, class, or ID specified in the SourceTrak application.

Number replacement happens in four places:

1. `document.body` - All elements are searched and matching numbers found in the text of each element are replaced. This includes elements that are hidden or otherwise not visible.
2. `<title>` - The page title, found in `<head>`, is searched and matching numbers are replaced
3. title attributes - Title attributes on elements, e.g. `<div title="(555) 555-5555">`, will be searched and have their numbers replaced
4. tel links - Any anchor tag in the format `<a href="tel:5555555555">` will be searched and have its matching tel number replaced. Numbers found in the actual content of the anchor tag are replaced in step 1.

### Number Matching

The script will match phone numbers in the following formats:

```
5554443333
555 444 3333
555-444-3333
555.444.3333
(555) 444 3333
(555) 444-3333
(555)-444-3333
(555).444.3333

15554443333
1-555-444-3333
1.555.444.3333
1 (555) 444 3333
1-(555)-444-3333
1 (555)-444-3333
1.(555).444.3333
1 (555).444.3333
```

The script will also match the format of each number it directly replaces. Numbers inserted by class or ID will default to the format `(555) 555-0001`.

Format matching for number replacement can be disabled by setting the global variable `_st_format = false` before the script loads, causing the SourceTrak number to use the default format.

### Example 1

This sample page demonstrates each instance where a number will be replaced. In the SourceTrak application, we've set our Phone Number Replacement options as follows:

![Number replacement options](https://raw.githubusercontent.com/themphill/number_replacement_docs/master/img.png)

```html
<html>
	<head>
		<title>555.555.0001</title>
	</head>

	<body>
		<div class="yourCustomClass">
		    This text will be replaced
		</div>

		<div>
		    <span id="yourCustomID">
		    	This text will be replaced
		    </span>
		</div>

		<img title="(555) 555-0001" />

		<div>
			Give us a call at <a href="tel:15555550001">1 555 555 0001</a>!
		</div>

		<div style="display:none;">
		    (555) 555-0001
		</div>

		<!-- SourceTrak snippet at the end of the body -->
		<script type="text/javascript">
		    var _stk = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0";

		    (function(){
		        var a=document, b=a.createElement("script"); b.type="text/javascript";
		        b.async=!0; b.src=('https:'==document.location.protocol ? 'https://' :
		        'http://') + 'd31y97ze264gaa.cloudfront.net/assets/st/js/st.js';
		        a=a.getElementsByTagName("script")[0]; a.parentNode.insertBefore(b,a);
		    })();
		</script>
	</body>
</html>
```

After number replacement, our page will look like this:

```html
<html>
	<head>
		<title>555.555.9999</title>
	</head>

	<body>
		<div class="yourCustomClass">
		    (555) 555-9999
		</div>

		<div>
		    <span id="yourCustomID">
		        (555) 555-9999
		    </span>
		</div>

		<img title="(555) 555-9999" />

		<div>
			Give us a call at <a href="tel:15555559999">1 555 555 9999</a>!
		</div>

		<div style="display:none;">
		    (555) 555-9999
		</div>

		<!-- SourceTrak snippet at the end of the body -->
		<script type="text/javascript">
		    var _stk = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0";

		    (function(){
		        var a=document, b=a.createElement("script"); b.type="text/javascript";
		        b.async=!0; b.src=('https:'==document.location.protocol ? 'https://' :
		        'http://') + 'd31y97ze264gaa.cloudfront.net/assets/st/js/st.js';
		        a=a.getElementsByTagName("script")[0]; a.parentNode.insertBefore(b,a);
		    })();
		</script>
	</body>
</html>
```

### Caveats

Numbers in images, JavaScript, Flash, or attributes of HTML elements will NOT be replaced.

The script only runs once; therefore, only numbers that are present in the document at the time the script executes will be replaced. Numbers added to the page after the script has executed will NOT be replaced.

Numbers inside "template scripts", i.e. `<script type="text/template">`, will NOT be replaced.

### Example 2

This example shows instances of numbers that will NOT be replaced:

```html
<html>
	<head>
		<title></title>
	</head>

	<body>
		<!-- Numbers will not be replaced in attributes like data -->
		<div data-phone="(555) 555-0001"></div>

		<script type="text/javascript">
			// Numbers will not be replaced in JavaScript
			var number = '(555) 555-0001';
		</script>

		<script type="text/template">
		    <!-- Numbers will not be replaced in template scripts -->
		    <div>
		        (555) 555-0001
		    </div>
		</script>

		<!-- SourceTrak snippet at the end of the body -->
		<script type="text/javascript">
		    var _stk = "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0";

		    (function(){
		        var a=document, b=a.createElement("script"); b.type="text/javascript";
		        b.async=!0; b.src=('https:'==document.location.protocol ? 'https://' :
		        'http://') + 'd31y97ze264gaa.cloudfront.net/assets/st/js/st.js';
		        a=a.getElementsByTagName("script")[0]; a.parentNode.insertBefore(b,a);
		    })();
		</script>
	</body>
</html>
```

### Accessing the SourceTrak Number

To facilitate custom use, the script provides access to the SourceTrak number via the global variable `_st` and the following two functions:

* `_st.getNumber()` - returns the SourceTrak number as an unformatted string
* `_st.getFormattedNumber()` - returns the SourceTrak number with the specified or default formatting

If `_st.getFormattedNumber()` is passed a phone number*, it will apply that format to the SourceTrak number, otherwise, it will use a default.

```javascript
_st.getNumber();
//=> 5555559999

_st.getFormattedNumber();
//=> (555) 555-9999

_st.getFormattedNumber('1 555.555.5555');
//=> 1 555.555.9999
```

Because the script is loaded asynchronously**, the SourceTrak number will NOT be available at DOM ready. To assist custom code wishing to use the SourceTrak number, the script will fire the `_st_ready` custom DOM event once the SourceTrak number is available. For browsers that do not support custom DOM events (IE8 and older), the `_st.isReady()` method is provided for scripts to check via polling.

```javascript
// native implementation
document.addEventListener('_st_ready', function () {
	_st.getNumber();
});

// it also works with jQuery
$(document).on('_st_ready', function () {
    _st.getFormattedNumber();
});

// checking the ready method after the script has loaded
_st.isReady();
//=> true
```

Note that `_st_ready` does not send any additional information with the event.

### Dependencies

SourceTrak is completely self-contained in the `_st` global object and has no external dependencies.


*: The supplied phone number will only be used for formatting the SourceTrak number for that particular instance, and will not otherwise affect SourceTrak in any way.

**: It is possible, though not recommended, to use SourceTrak synchronously. However, this is an advanced use case and is not covered in this document.
