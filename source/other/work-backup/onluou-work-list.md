# Bug fixed lists for onlyou
## 1. Datepicker plugin is covered by other popup
Setting zIndex for element rendered to be datepicker.

## 2. The browser file button for jqplupload can't work only in edge browser
The bug has occured because the `edge` doesn't support some features.
Those features are defined in `plupload` plugin and checked by `I.can('summon_file_dialog')` statement written in `moxie.js` on line 6530.
So if browser button run this plugin in runtime of `html5` mode, plugin will not bind click event to this button.
When this situation happened,you can full cover above this button by a input  generated  by plugin  to solve this problem.

But, if the button element has't content(i mean it's calculated width is 0) and you set this button content after the plugin
has been initialized,input can't cover above this button.

Therefore,in order to solve this bug you should set the button's content before plugin has been initialized.

## 3. AutoSelect can't be activated when clicking icon
Label should set "for" attribute for input.

## 4. IE doesn't support to generate a date using `new Date("")` format. eg. new Date("2015/1/1")
For IE,you can use `new Date(2015,1,1)` or date.setUTCFullYear(2105,1,1) and date.setUTCHours(0, 0, 0, 0) as same; 

## 5."Input" event get fired when setting/unsetting the placeholder in IE(10~11)
Any of the actions below can cause the problem
* 1. Focus the input element 
* 2. Setting/unsetting the placeholder property
* 3. Generating the input element dynamically with placeholder property and binding 'input' event

For  action 1 and 3,you can bind event on nextTick eg:
```javascript
setTimeout(function () {
  element.addEventListener('input',handler)
}, 0);
```
                                           
