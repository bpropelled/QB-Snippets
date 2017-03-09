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
