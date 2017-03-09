# QB-Snippets
My collection of useful Quickbase Snippets

## Using Promises in QB

```javascript
var dbidp = "your dbidp";
var qidp = "your qidp"

var dbidc = "your dbidc";
var related_parent_fid_c = "your related parent fid";

var promisep, promisec;

promisep = $.post(dbid1,{
  act:"API_PurgeRecords",
  qid: qidp
});

$.when(promisep).then(function(){
  promisec = $.post(dbid2,{
    act:"API_PurgeRecords",
    query: "{'" + related_parent_fid_c + "'.XEX.''}"
  });
});

$.when(promisec).then(function(){
  alert("parent and child records deleted");
});
```

## The ON Failure Technique -> coined by Nathan Thompson of WEG
This technique is similar to the image on load technique 

### Step 1 - Create the User Defined Variables
Name | Value
---- | -----
of | <img style='Display:None' src=' ' id='canTarget' onerror="javascript:$.getScript(gReqAppDBID+'?a=dbpage&pagename=
/of | ');">

### Step 2 - Use - create a custom code page and add it like this
```html
[of] & "customCodePage.js" & [/of]
```
