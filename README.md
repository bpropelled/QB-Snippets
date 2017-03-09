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
### And Another more simplified version
```javascript
var dbid = "";
var apptoken = "";
$.ajaxSetup({data: {apptoken: apptoken}});

$.get(dbid, {
  act: "API_DoQuery",
  qid: "1",
  clist: "3",
  slist: "3",
  options: "sortorder-D.num-1"
}).then(function(xml) {
  var maxRid = $("record_id_", xml).text();
  console.log(maxRid);
});
```

## The ON Failure Technique 
##### -> coined by Nathan Thompson of WEG (https://azastudio.net/consulting-1/2016/12/26/creating-tabs-in-quickbase)
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

## More Control over individual state pages in QB
#### Create this as a code page and reference it using IOL of OF techniques
```javascript
(function(){
  var querystring=document.location.search;

  if (/dlta=mog/i.test(querystring)) {
    //GRID EDIT PAGE ========================================
    //alert("You are on the Grid Edit Page");

  } else if(/a=er/i.test(querystring)) {
    //EDIT RECORD PAGE ========================================
    //alert("You are on the Edit Record Page");

  } else if (/a=API_GenAddRecordForm/i.test(querystring)) {
    //API_GenAddRecordForm PAGE ========================================
    //alert("You are on the GenAddRecordForm Page!");

  } else if (/GenNewRecord/i.test(querystring)) {
    //ADD RECORD PAGE ========================================
    //alert("You are on the Add Record Page");

  } else if (/nwr/i.test(querystring)) {
    //ADD RECORD PAGE ========================================
    alert("You are on the Add Record Page");

    var markup = "";
    markup += "<ul>";
    $("#formContents div.sectionDiv").each(function(index){
      markup += "<li><a href='#tab_" + (index+1) + "'>" + $("span.sectionTitle",this).text() + "</a></li>";
    });
    markup += "</ul>";

    $("#formContents").prepend(markup);

    $("#formContents div.sectionDiv").each(function(index){
      $(this).attr("id","tab_" + (index+1));
      $("div:lt(2)",this).hide();
    });

    $("#formContents").tabs();

  } else if(/a=dr/i.test(querystring)) {
    //DISPLAY RECORD PAGE
    //alert("You are on the Display Record Page");
    $("img[qbu=module]").closest("td").css("background-color","#FFFFFF");

  } else if(/a=q/i.test(querystring)) {
    //REPORT PAGE ========================================
    //alert("You are on the Report Listing Page");

  } else if(/a=td/i.test(querystring)) {
    //TABLE DASHBOARD PAGE ========================================
    //alert("You are on the Table Dashboard Page");

  } else {
    //OTHER PAGE ========================================
    //alert("You are on the Some Other Page");
  }

})();
```


#### The ultimate resource of QB snippets from the Dan - Contact and recognition below
[Pasties](https://haversineconsulting.quickbase.com/db/bgcwm2m4g?a=td)

:+1: :+1: :+1::+1: :octocat:
> All rights reserved. Some of the code in this page is copyright by Dan Diebolt (734-985-0721) (dandiebolt@yahoo.com).