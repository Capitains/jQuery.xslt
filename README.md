jQuery CTS XSLT Helper
===

[![Build Status](https://travis-ci.org/Capitains/jQuery.xslt.svg?branch=master)](https://travis-ci.org/Capitains/jQuery.xslt)

In all example and documentation, "target element" or $(target) is the element on which ctsXSLT have been started. To start a XSLT on a given container - thus filling it with the form -, use the following expression : `$(element).ctsXSLT(XSLTName, options)`

## XSLTs

See implemented XSLTs documentations.

## API

- `$(target).ctsXSLT(XSLTName, "getValues")` : returns a dictionary representing the form key-value's pair
- `$(target).ctsXSLT(XSLTName, "instance")` : return the CTS.XSLT abstraction object
## Events

| Name                   | Description
|-------------------------------|---------------------------------
| cts-xslt:XSLTName:doing | Before sending request to XSLT naed "XSLTName"
| cts-xslt:XSLTName:done  | When sending request to XSLT named "XSLTName" is done

## Options

Options are set-up as the second argument of `$(element).ctsXSLT()`. It's a simple dictionary

- `endpoint` (Type : string)
Endpoint of XSLT is the URI of the XSLT sheet you want to use.
- `xml` (Type . string or jQuery or function)
The value of `xml` can either be a selector such as `".xml"` or a jQuery object such as `$(".xml")`. When transforming data, the plugin will used the targeted element value as the source fore the transformation.
- `css` (Type : dictionary)
See [below](#CSS-custom-classes)
- `trigger` (Type : string)
When given *trigger* is called on the original element, the XSLT attached to it will be called.
- `click` (Type : jQuery or string)
The value of `click` can either be a selector such as `".button"` or a jQuery object such as `$(".button")`. It will attach an event listener to id. Clicking the targeted element will call the attached XSLT.
- `driver` (Type : dictionary)
`driver` dictionary allows you to keep the plugin to generating form field in the DOM by giving it a replacement. The keys should be the name of the field, the value can be : function, jQuery object or selector.
**eg** : We instantiate CTS.XSLT "llt.seg_to_tok" which has fields name : e_lang, e_format, e_attachtoroot. Instead of generating new DOM, we can give it the following driver parameters.
```javascript
driver : {
    e_lang : function() { return "<hello></hello>"; },
    //Here we give a replacement function
    e_format : ".hiddenInput"
    //Here we pass a selector. The XSLT will use it to retrieve the value of the element.
    e_attachtoroot : $(".checkbox")
    //Here we pass a jQuery object. The XSLT will use it as well to retrieve the value of this element
}
```
- `defaults` (Type : dictionary)
`defaults` dictionary allows you to override the XSLT field's default value. The keys should be the name of the field, the value can be string, list or boolean.
**eg** : We instantiate CTS.XSLT "llt.seg_to_tok" which has fields name (default value): e_lang("latin"), e_attachtoroot(false). In our case, we want merging to be true by default:
```javascript
defaults : {
    e_attachtoroot : true
}
```
The generated DOM form will contain this value as default. For example, if e_attachtoroot is a checkbox, it will be checked.
- `names` (Type: dictionary)
Default behaviour of DOM generation in CTS.XSLT will create anonymous inputs (*ie* : input would have no name attribute). `names` dictionary allows you to give to a given field a name. The keys should be the name of the field, the value should be a string.
**eg** : We instantiate CTS.XSLT "llt.seg_to_tok" which has fields name : e_lang, e_attachtoroot.
```javascript
defaults : {
    e_attachtoroot : "e_attachtoroot"
}
```
It will produce the following inputs :
```html
<input type="text" /> <!-- this is e_lang field -->
<input type="checkbox" name="e_attachtoroot" /> <!-- this is e_attachtoroot field -->
```
- `show` (Type : Boolean, String)
If `show` is a boolean, the fiedset generated by ctsXSLT will be shown only if show is true. If this is a string, the original element will toggle its visibility when `show` value is triggered on it.
*eg* :
```javascript
$(".element").ctsXSLT("llt.seg_to_tok", {
    show : "trigger"
});
$(".element").hide();
$(".element").trigger("trigger"); // Will show element
```
- `callback` (Type : function)
Callback is called when XSLT is called and has answered. Parameter passed to this function are value returned by the XSLT transform.

### Recap
| Key              | type           | Default       | Informations
| ---------------- | -------------- | ------------- | -----------------------------------
| endpoint         | string         | ""            | URI of the stylesheet
| xml              | jQuery,string  |               | Element from which to retrieve the xml to transform
| css              | object         | {}            | See below [CSS custom classes](#css-custom-classes)
| trigger          | string         | null          | Send request when trigger is trigger on original element
| click            | string,element | false         | Send request when click is triggered on targeted element
| driver           | dictionary     | {}            | Instead of generating DOM, reuse an element in the DOM or a function for a given parameter
| defaults         | dictionary     | {}            | Override a default value of the XSLT
| names            | dictionary     | {}            | When generating DOM inputs, give to a field a given name.
| show             | boolean,string | true          | Hide/Show the element. If a string, $(target).trigger(the string) will toggle the visibility of the form
| callback         | function       | null          | Function to call on XSLT calling. Response from ajax request is passed as data to the callback function


## CSS custom classes

Because so many plugins force you to dig the code or extend your already existing CSS classes, we thought it might be cool to give you the availability to add your own classes to **all the generated DOM elements !**. To do so, when passing your `option` object to the constructor, add the `css` dictionary, where keys are identifier described below and value a list of classes (**WITHOUT THE DOT**).

|      Identifier          |  Automatic class                   | Description
|--------------------------|------------------------------------|--------------
| container                | `["cts-xslt"]`                     | Container for all the generated DOM (`<fieldset />`)
| container-legend         | `["cts-xslt-legend"]`              | Container for the title of the XSLT (`<legend />`)
|                          |                                    |
| field-container          | `[]`                               | `<div />` containing a field
| field-label              | `[]`                               | `<Label />` of the field
| field-input-container    | `[]`                               | `<div />` wrapping the input inside the field-container
| field-text               | `[]`                               | Classes for `<input type="text" />`
| field-checkbox           | `[]`                               | Classes for `<input type="checkbox" />`
| field-textarea           | `[]`                               | Classes for `<textarea />`

## Example 

```html
<html>
<head>
<meta http-equiv="content-type" content="text/html; charset=utf-8" />
<script type="text/javascript" src="../src/cts.js"></script>
<script type="text/javascript" src="../src/modules/utils.js"></script>
<script type="text/javascript" src="../src/modules/repository.js"></script>
<script type="text/javascript" src="../src/modules/text.js"></script>
<script type="text/javascript" src="../src/modules/i18n.js"></script>
<script type="text/javascript" src="../src/modules/service.js"></script>
<script type="text/javascript" src="../src/modules/xslt.js"></script>
<script type="text/javascript" src="../src/i18n/en.js"></script>
<!-- Dependencies -->
<script type="text/javascript" src="../node_modules/jquery/dist/jquery.min.js"></script>
<!-- Plugins using dependencies -->
<script type="text/javascript" src="../src/plugins/jquery.cts.xslt.js"></script>
<script type="text/javascript">
    $(document).ready(function() {
        $("#lltsegtok").ctsXSLT("llt.segtok_to_tb", {
            "endpoint" : "./test_data/segtok.xslt",
            "xml" : $(".segtok"),
            "names" : {
                "e_lang" : "lang"
            },
            "DOM" : {
                //"text" : $(".TEItext")
            },
            "click" : ".button",
            "trigger" : "tokenize!",
            "callback" : function(data) {
                $(".segtok").val(data);
            },
            "show" : "SHOW_ME_THE_GONDOR"
        });
        $("#lltsegtok").on("cts-service:llt.tokenizer:done", function() {
            //console.log(data);
        });
        $(".toggle_button").on("click", function(e) {
            e.preventDefault();
            $("#lltsegtok").trigger("SHOW_ME_THE_GONDOR");
        });
        $(".trigger_button").on("click", function(e) {
            e.preventDefault();
            $("#lltsegtok").trigger("tokenize!");
        });
    });
</script>
</head>
<body>
    <button class="button">Click me !</button> 
    <button class="trigger_button">Trigger me !</button> 
    <button class="toggle_button">Toggle me !</button>
    <div id="lltsegtok"></div>
    <textarea class="segtok"><?xml version="1.0" encoding="UTF-8"?><llt-segtok><TEI xmlns="http://www.tei-c.org/ns/1.0"><text xml:lang="grc"><body><div1 n="1" type="Book"><l n="1"><milestone ed="P" unit="para"/><w n="1" s_n="1"><w s_n="1" n="1">μῆνιν</w></w><w n="2" s_n="1"><w s_n="1" n="2">ἄειδε</w></w><w n="3" s_n="1"><w s_n="1" n="3">θεὰ</w></w><w n="4" s_n="1"><w s_n="1" n="4">Πηληϊάδεω</w></w><w n="5" s_n="1"><w s_n="1" n="5">Ἀχιλῆος</w></w></l></div1><div1 n="1" type="Book"><l n="2"><w n="6" s_n="1"><w s_n="1" n="6">οὐλομένην</w></w><pc n="7" s_n="1"><pc s_n="1" n="7">,</pc></pc><w n="8" s_n="1"><w s_n="1" n="8">ἣ</w></w><w n="9" s_n="1"><w s_n="1" n="9">μυρίʼ</w></w><w n="10" s_n="1"><w s_n="1" n="10">Ἀχαιοῖς</w></w><w n="11" s_n="1"><w s_n="1" n="11">ἄλγεʼ</w></w><w n="12" s_n="1"><w s_n="1" n="12">ἔθηκε</w></w><pc n="13" s_n="1"><pc s_n="1" n="13">,</pc></pc></l></div1><div1 n="1" type="Book"><l n="3"><w n="14" s_n="1"><w s_n="1" n="14">πολλὰς</w></w><w n="15" s_n="1"><w s_n="1" n="15">δʼ</w></w><w n="16" s_n="1"><w s_n="1" n="16">ἰφθίμους</w></w><w n="17" s_n="1"><w s_n="1" n="17">ψυχὰς</w></w><w n="18" s_n="1"><w s_n="1" n="18">Ἄϊδι</w></w><w n="19" s_n="1"><w s_n="1" n="19">προΐαψεν</w></w></l></div1><div1 n="1" type="Book"><l n="4"><w n="20" s_n="1"><w s_n="1" n="20">ἡρώων</w></w><pc n="21" s_n="1"><pc s_n="1" n="21">,</pc></pc><w n="22" s_n="1"><w s_n="1" n="22">αὐτοὺς</w></w><w n="23" s_n="1"><w s_n="1" n="23">δὲ</w></w><w n="24" s_n="1"><w s_n="1" n="24">ἑλώρια</w></w><w n="25" s_n="1"><w s_n="1" n="25">τεῦχε</w></w><w n="26" s_n="1"><w s_n="1" n="26">κύνεσσιν</w></w></l></div1><div1 n="1" type="Book"><l n="5"><w n="27" s_n="1"><w s_n="1" n="27">οἰωνοῖσί</w></w><w n="28" s_n="1"><w s_n="1" n="28">τε</w></w><w n="29" s_n="1"><w s_n="1" n="29">πᾶσι</w></w><pc n="30" s_n="1"><pc s_n="1" n="30">,</pc></pc><w n="31" s_n="1"><w s_n="1" n="31">Διὸς</w></w><w n="32" s_n="1"><w s_n="1" n="32">δʼ</w></w><w n="33" s_n="1"><w s_n="1" n="33">ἐτελείετο</w></w><w n="34" s_n="1"><w s_n="1" n="34">βουλή</w></w><pc n="35" s_n="1"><pc s_n="1" n="35">,</pc></pc></l></div1></body></text></TEI></llt-segtok></textarea>

</body>
</html>
```