# SourceTrak Number Replacement

## Summary

By default, when SourceTrak loads on a page, it will replace all the numbers (that match the provided replacement number) it finds in the page title and the text of the page body, including "tel" links[^1] and title attributes. Number replacement only happens once, so any numbers added to the page afterwards will not be replaced. Numbers present in images, JavaScript, or in HTML (except as noted above) will also not be replaced.

For use cases not covered by the default behavior, the SourceTrak number is made available in the SourceTrak JavaScript code running on the page. This allows customers to write their own JavaScript code that can access the number for custom use.

[^1]: In HTML, a "tel" link is of the format `<a href="tel:5555555555"></a>`. The number "5555555555" would be replaced.

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

### Example 1

This sample page demonstrates each instance where a number will be replaced. In the SourceTrak application, we've set our Phone Number Replacement options as follows:

![Number replacement options](https://raw.githubusercontent.com/themphill/number_replacement_docs/master/img.png)

```html
<html>
	<head>
		<title>(555) 555-0001</title>
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
			Give us a call at <a href="tel:5555550001">(555) 555-0001</a>!
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
		<title>(555) 555-9999</title>
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
			Give us a call at <a href="tel:5555559999">(555) 555-9999</a>!
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
		<div data-phone="(555) 555-0001"></div>
		
		<script type="text/javascript">
			var number = '(555) 555-0001';
		</script>
		
		<script type="text/template">
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
* `_st.getFormattedNumber()` - returns the SourceTrak number formatted as `(555) 555-5555`

Note that because the script is loaded asynchronously[^1], the SourceTrak number will not be available at DOM ready. Custom code wishing to use the SourceTrak number should check for the existence of `_st` before attempting to access the number.

### Dependencies

SourceTrak is completely self-contained in the `_st` global object and has no external dependencies.


[^1]: It is possible, though not recommended, to use SourceTrak synchronously. However, this is an advanced use case and not covered in this document.
