# Tampermonkey Script API Reference


[English](README-en.md) | [中文](README.md)


This article organizes most of the Tampermonkey script API usage and includes code examples for each method. In addition you can install the [user script example](https://github.com/examplecode/user-script-example)  to test them in a browser that supports user scripts.



> Tip: The API support  described in the documentation are based on the built-in script manager of [XBrowser](https://en.xbext.com), other script managers may have slight differences.



## Meta data

Metadata is usually placed at the beginning of a script, and its main function is to declare, set, and describe the script, including the script name, introduction, author, version number, third-party library dependencies, and which APIs are used, etc.

The following is an example of a script's metadata declaration.

```javascript
// ==UserScript==
// @name         Say hello
// @namespace    com.example.hello
// @version      0.1
// @description  When you open the site example.com it says "HELLO"
// @author       You
// @match        www.example.com
// ==/UserScript==
```



The following are the metadata definition specifications supported by the  Browser

| Tag          | Description                                                  |
| ------------ | ------------------------------------------------------------ |
| @name        | Name of the script                                           |
| @namespace   | The namespace of the script, either as a unique identifier or as a URL |
| @description | Introduction to the script describing the usage and functions of the script, etc. |
| @icon        | Customize an icon for the script to be displayed in the script manager   as well as in the browser extension menu. This can be a url icon resource or a Base64 encoded Data URI. |
| @author      | Name or nickname of the author of the script                 |
| @version     | Current script version number                                |
| @match       | Define the scope of the script to execute the script only at the matching URL or domain, this tag can have multiple lines declared in the metadata |
| @include     | Similar to @match, it is used to describe the scope of a script and can have multiple lines of declaration in the metadata. |
| @exclude     | Used to exclude some URLs, even if @match and @include have specified a match, there can be a multi-line declaration in the metadata. |
| @require     | Specify third-party libraries that the script needs to depend on before it can be executed, and there can be a multi-line declaration in the metadata |
| @resource    | The script execution needs to depend on some resource files, such as css, text, image resources, etc., which can be declared on multiple lines in the metadata |
| @run-at      | Specify the timing of script execution, different scenarios may require different execution timing, where the value of @run-at can be found in the following table |
| @grant       | Declare which API functions are used ,  there can be multiple lines in the metadata. |

The metadata tag @run-at has the following attribute values.

| Value          | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| document-start | Specify that the script is executed at the beginning of the DOM tree, add this statement if you need the script to be executed early. |
| document-end   | Specify that the script is executed when the DOM data is loaded |
| document-idle  | Execute when the page is loaded. When the metadata does not have a @run-at declaration, the script is executed at this time by default. |
| main-menu      | Extension tag declaration for XBrowser, means that the script is not executed automatically but through the extended main menu option |
| context-menu   | Extension tag declaration for XBrowser,  means that the script is not executed automatically but through the extended long press menu option |
| tool-menu      | Extension tag declaration for XBrowser,  means that the script is not executed automatically but through the extended tools menu option |



## User Script API 


### GM_addStyle

#### Description

Adds a CSS style to the page.

#### Syntax

```javascript
function GM_addStyle (cssString)
```

#### Parameters


| Name      | Type   | Description        |
| --------- | ------ | ------------------ |
| cssString | String | Stylesheet strings |



#### Example


```javascript
 GM.addStyle('#note{color: white; background: #3385ff!important;border-bottom: 1px solid #2d7');
```


### GM_addElement

#### Description

Add a page element that can specify a parent node without specifying the parent root node as its parent.

#### Syntax

```javascript
function GM_addElement(tagName, attributes)
```

Or

```javascript
function GM_addElement(parentNode,tagName, attributes)
```

#### Parameters

| Name       | Type   | Description             |
| ---------- | ------ | ---------------- |
| tagName    | String | Name of element |
| attributes | Object | Property name/value pairs |
| parentNode | Object | Parent node of the newly created element |

#### Example

```javascript

GM_addElement('script', {
  textContent: 'window.foo = "bar";'
});

GM_addElement('script', {
  src: 'https://example.com/script.js',
  type: 'text/javascript'
});

GM_addElement(document.getElementsByTagName('div')[0], 'img', {
  src: 'https://example.com/image.png'
});

GM_addElement(shadowDOM, 'style', {
  textContent: 'div { color: black; };'
});
```



### GM_setValue

#### Description

Save a key value/data to browser local storage.

#### Syntax

```javascript
function GM_setValue(name,value)
```

#### Parameters

| Name  | Type     | Description                                                  |
| ----- | -------- | ------------------------------------------------------------ |
| name  | String   | Name of key                                                  |
| value | Any type | Can be any data type such as integers, strings, boolean types, objects, etc. |

#### Example

```javascript
GM_setValue("foo", "bar");
GM_setValue("count", 100);
GM_setValue("active", true);
GM_setValue("data", {
  name: 'Andy',
  age: 18
});
```

### GM_getValue

#### Description

Get data from browser local storage by specified key value

#### Syntax

```javascript
function GM_getValue(name, defaultValue)
```

#### Parameters

| Name         | Type     | Description                                      |
| ------------| --------| ----------------------------------------- |
| name         | String   | Name of key                                  |
| defaultValue | Any type | Optional, returns the default value if the key value has never been set       |

#### Return Value

Returns the data  that was originally set.


#### Example

```javascript
GM_setValue("foo", "bar");
GM_setValue("count", 100);
GM_setValue("active", true);
GM_setValue("data", {
  name: 'Andy',
  age: 18
});

var info = `foo = ${GM_getValue("foo")}
          count = ${GM_getValue("count")}
          active = ${GM_getValue("active")}
          data.name =  ${GM_getValue("data").name}`;                   
alert(info);
```


### GM_listValues

#### Description

Returns the list of key values set using GM_setValue.

#### Syntax

```javascript
function GM_listValues()
```

#### Example


```javascript
GM_setValue("foo", "bar");
GM_setValue("count", 100);
GM_setValue("active", true);
GM_setValue("data", {
name: 'Andy',
age: 18
});
alert(GM_listValues());
```


### GM_deleteValue

#### Description

Delete the key value set by the GM_setValue method.

#### Syntax

```javascript
function GM_deleteValue(name)
```

#### Parameters

| Name | Type   | Description |
| ---- | ------ | ----------- |
| name | String | Name of key |

#### Example


```javascript 
GM_deleteValue("foo");
```

``` javascript
let keys =  GM_listValues();
for (let key of keys) {
  GM_deleteValue(key);
}
```

### GM_addValueChangeListener

#### Description

Used to listen to key value change  of key-value pair set by GM_setValue

#### Syntax

```javascript
function GM_addValueChangeListener(name,callback);

```

#### Parameters   

| Name     | Type              | Description                                                  |
| -------- | ----------------- | ------------------------------------------------------------ |
| name     | String            | String key name                                              |
| callback | Callback function | Call back this function when the specified key name value changes |

##### callback parameters

- **name** - String，Key name
- **oldValue** - Any type ，OldValue
- **newValue** - Any type , New value
- **remote** - Boolean type，The callback from the current tab or the callback from the other tab (cross tab callback is not implemented at present).



#### Return value

Callback id returned by GM_addValueChangeListener

#### Example

``` javascript
   listener_id = GM_addValueChangeListener("foo",function(name,old_value,new_value,remote) {
        alert("Value Changed:" + name + ":" + old_value + "=>" + new_value);
    });
```


### GM_removeValueChangeListener

#### Description

Used to delete  listener callback added by GM_addValueChangeListener

#### Syntax

```javascript
function GM_removeValueChangeListener(listener_id);

```

#### Parameters   

| Name | Type | Description              |
| -------- | -------- | ---------------------------- |
| listener_id | String | Callback id returned by GM_addValueChangeListener |



#### Example

``` javascript
 GM_removeValueChangeListener(listener_id);
```

### GM_notification
#### Description
Display a notification message
#### Syntax
```javascript
function GM_notification(details)
```
Or

```javascript
function GM_notification(text, title, image, onclick )
```
#### Parameters

| Name    | Type              | Description                                                  |
| ------- | ----------------- | ------------------------------------------------------------ |
| details | Object            | An object containing a text field and ondone, onclick callback function fields |
| text    | String            | Text Content                                                 |
| title   | String            | Parameters are not currently implemented on the mobile side for compatibility |
| Image   | Object            | Parameters are not currently implemented on the mobile side for compatibility |
| onclick | Callback function | Callback function when the user has clicked the OK button    |



#### Example

```javascript
GM_notification("Hello!");

GM.notification({
  text: 'This is a message with callback',
  onclick: function() {
    alert("you click message ok button");
  },
  ondone: function() {
    alert("message bar closed");
  }
});

GM_notification("Hello","","",function() {
  alert("you click message ok button");
})

```

### GM_setClipboard
#### Description
Write string data to the clipboard
#### Syntax
```
function GM_setClipboard(data)
```
#### Parameters

| Name | Type   | Description  |
| ---- | ------ | ------------ |
| data | String | Text content |


#### Example
```javascript
GM_setClipboard('this is test data');
```

### GM_registerMenuCommand

#### Description

Register a menu option, which will be displayed in the XBrowser's Page Tools menu.

#### Syntax
```javascript
  function GM_registerMenuCommand(title,callback) 
```

#### Parameters

| Name | Type | Description |
| ----- | ------ | -------- |
| title | String | Menu item name |
| callback | Callback function | Callback functions executed by clicking on menu items |

#### Return Value

Returns the command ID of the menu item, which is used when un register of the menu

#### Example

```javascript
GM_registerMenuCommand("click me",function() {
  alert("You click menu item");
});
```

### GM_unregisterMenuCommand
#### Description

Unregister previously registered menu items

#### Syntax
```javascript
function GM_unregisterMenuCommand(commandId) 
```

#### Parameters

| Name | Type | Description |
| ----- | ------ | -------- |
| commandId | String | Command id of the menu item |


#### Example
```javascript
GM_unregisterMenuCommand(commandId);
```

### GM_openInTab
#### Description
Open a page in a new tab

#### Syntax
```javascript
function GM_openInTab(url,background) 
```
#### Parameters

| Name | Type | Description |
| ----- | ------ | -------- |
| url | String | URL of the new tab |
| background | Boolean | Whether to open the tab in the background, default is false |


#### Example

```javascript
GM_openInTab("https://www.example.com");
GM_openInTab("https://www.example.com",true);
```

### GM_download

#### Description
Call the browser's default downloader to download

#### Syntax
```javascript
function GM_download(url,name) 
```
Or

```javascript
function GM_download(detail) 
```

#### Parameters

| Name | Type | Description |
| ----- | ------ | -------- |
| url | String | To download resources URL |
| name | String | Name of the downloaded file |
| detail | Object | Configuring download parameters via detail objects |

##### detail  property list

- **url**  - String type, indicating the URL to be downloaded
- **name** - String type, name of the downloaded file 
- **confirm** - Boolean type, whether to pop up the download dialog, set this option to false when batch download
- **tag** -  Tagging of downloaded files  . Files with the same tag are saved in the directory named after the tag.

#### Example

```javascript
GM_download("https://www.xbext.com/download/xbrowser-release.apk") 
```

```javascript
//Specify the name of the download  file
GM_download("https://www.xbext.com/download/xbrowser-release.apk,"xbrowser.apk");
```

```javascript
//Batch downloads, the files are saved in a subdirectory named "test-file" in the download directory.
let urls = ["https://www.dundeecity.gov.uk/sites/default/files/publications/civic_renewal_forms.zip",
            "https://www.dundeecity.gov.uk/sites/default/files/publications/civic_renewal_forms.zip",
            "https://www.dundeecity.gov.uk/sites/default/files/publications/civic_renewal_forms.zip",
           ];
var i =0;
for(let url of urls ) {
  GM_download({
    url: `${url}`,
    name: `test-file${++i}.zip`,
    confirm: false,
    tag: "test-file"
  });
}
```




### GM_getResourceText

#### Description

Get the content of the URL resource defined by the metadata tag @resource

#### Syntax
```javascript
function GM_getResourceText(name)
```
#### Parameters

| Name | Type | Description |
| ----- | ------ | -------- |
| name | String | The name of the key defined by the @resource tag to refer to the resource |

#### Example

```javascript
// @resource     main-content https://www.example.com/res/main-content.txt
var text = GM_getResourceText("main-content");
```

#### Retun value

Returns the text content of the resource URL.



### GM_getResourceURL

#### Description

Gets the content of the resource the metadata tag @resource referring , which is encoded in Base64 and formatted as a Data URI.

#### Syntax

```javascript
function GM_getResourceURL(name)
```

#### Parameters

| Name | Type   | Description                                                  |
| ---- | ------ | ------------------------------------------------------------ |
| name | String | The name of the key defined by the @resource tag to refer to the resource |

#### Example

```javascript
 var img = document.querySelector("#avatar")
 //@resource     avatar01 https://api.multiavatar.com/avatar01.svg
 img.src = GM_getResourceURL("avatar01");
```

#### Return value

Returns the Data URI encoded in Base64.



### GM_xmlhttpRequest

#### Description

This method is similar to the [XMLHttpRequest](http://developer.mozilla.org/en/docs/XMLHttpRequest) object, the difference is that this method supports cross-domain requests, breaking the [Same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy), more flexible to use.

#### Syntax

```javascript
function GM_xmlhttpRequest(details)
```



#### Parameters

This method has only one parameter of object type ，The list of properties of the object and their meanings are as follows.

| Name    | Type   | Description                                           |
| ------- | ------ | ----------------------------------------------------- |
| details | Object | Contains a series of properties as control parameters |

#####  details properties

- **method** - Http request method, GET, POST, HEAD, etc.，GET、POST、HEAD etc.
- **url** - String，Target request URL.
- **headers** - Optional, String, HTTP protocol header, User-Agent, Referer, etc.
- **data** - Optional, string, data sent via POST method
- **responseType** - Optional, string, the response type, which can be one of arraybuffer, blob, json and stream.
- **onabort**- Optionally, a callback function when the HTTP request is abort.
- **onerror**- Optional, callback function that is called when an exception occurs on an HTTP request
- **onloadstart** - Optional, callback function where the HTTP request starts to be called
- **onreadystatechange** - Optional, callback function that is called when the status of an HTTP request changes
- **onload** - Optional, callback function that is called when the HTTP request is completed, the callback function parameter carries several properties as follows
  - **finalUrl** - The URL address of the final HTTP request, such as the URL after a redirect.
  - **readyState** -  Integer type, request status
  - **status** - HTTP Response Status
  - **statusText** - The text corresponding to the HTTP response status
  - **responseHeaders** - HTTP response headers
  - **response** - The  response object returned by the HTTP response，object type data, depending on the setting of the **responseType** field.
  - **responseText** - The text content returned by the HTTP response

#### Example



```javascript
//Start a HTTP get request
GM_xmlhttpRequest({
  method: "GET",
  url: "http://www.example.com/",
  onload: function(response) {
    alert(response.responseText);
  }
});
```



```javascript
//Start a HTTP post request
GM.xmlHttpRequest({
  method: "POST",
  url: "https://www.example.net/login",
  data: "username=johndoe&password=xyz123",
  headers: {
    "Content-Type": "application/x-www-form-urlencoded"
  },
  onload: function(response) {
    if (response.responseText.indexOf("Logged in as") > -1) {
      location.href = "http://www.example.net/dashboard";
    }
  }
});
```





###  GM_info

This is an object that holds the environment variables associated with each script, such as the script's version, author, introduction, etc. The list of object properties is as follows.

- **script** - Object type, contains some of the following properties.
  - **author** -  Author of this script
  - **name** - Name of this script
  - **description** - Script Description
  - **version** - Version
  - **copyright** - Copyright Information
  - **includes** - Array type, list of matching pages
  - **matches** - Array type, similar to includes, matching the list of pages
  - **excludes** - Array type, exclude URL list
  - **resources** - Array type, resource list
- **version** - Version of Script Manager
- **scriptHandler** - Name of the script manager

- **scriptMetaStr** - Script Manager Metadata String



#### Example

```javascript
var info = "Script Name: "  + GM_info.script.name + 
    "\nVersion: " + GM_info.script.version + 
    "\nVersion: " + GM_info.script.version + 
    "\nScriptHandler: " + GM_info.scriptHandler + 
    "\nScript Handler Version : " + GM_info.version ;

alert(info);
```




## References


- https://www.tampermonkey.net/documentation.php

- https://wiki.greasespot.net/Greasemonkey_Manual:API

- https://github.com/examplecode/user-script-example
