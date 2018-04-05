# PHP API Exmaples for QB

The PHP Quickbase API wrapper, originally written by [Joshua McGinnis](http://joshuamcginnis.com/), is a layer between the Quickbase API and PHP. It allows you to make API calls with PHP and is a lot cleaner than [cURLing](http://php.net/manual/en/book.curl.php) everything yourself.

I have been working with this API since the start of my career and have had to learn a lot on my own as there aren’t many places with guides for the API. In an effort to help other developers starting out with with this API I have written this article.

This article is going to cover the basic functionality of the wrapper and how to utilize it in your applications.

Firstly, get the latest version of the PHP wrapper from [GitHub](https://github.com/QuickbaseAdmirer/QuickBase-PHP-SDK).

## Getting an error related to CORS and Allowing the Origin
I started getting this error from the api saying the allow origing wasn't in the header, here is the fix
```php
header('Access-Control-Allow-Origin: https://realmname.quickbase.com');
//or wildcard the header
header('Access-Control-Allow-Origin: *');
```
## Creating the Quickbase Object

```php  
include_once('quickbase.php');//include the api file

  //Variables to hold our account info
  $qbUser     = 'myQbUser';
  $qbPassword = 'mySecurePassword123';
  $qbAppToken = 'asdfasdf1231511221qwert';

  $qb = new QuickBase($qbUser, $qbPassword, true, '', $qbAppToken, 'mycompany', '');//creating the object
```

So there’s a bit going on here. Let’s break it down by looking at the constructor for the Quickbase class.

```php 
public function __construct($un, $pw, $usexml = true, $db = '', $token = '', $realm = '', $hours = '') {...}
```

The constructor takes 7 arguments. We don’t need to provide all of them.

*   **$un** and **$pw** are fairly self-explanatory. These will be your quickbase accounts credentials.
*   We want to leave **$usexml** as true. This ensures the responses come back as XML. The API wrapper is set up to parse the XML for us so let’s just leave this alone.
*   **$db** is the database id. You can provide this now if you are only working with one table, but, I set this later using the object’s property.
*   **$token** is very important. You must first generate a token for your Quickbase application. To do this, log into Quickbase and go to the application you wish to write some code for. Once you are in the application, go to settings in the upper left of the screen, or https://[MYREALM].quickbase.com/db/[MY APPLICATION ID]?a=AppSettingsHome. You will then want to go to “App Properties” and click on “Manage Application Token”. From here you can generate an app token.
*   **$realm** is where your companies applications live. This will be set up already most likely. To find your realm, log into quickbase and look at the subdomain of the url. ie) https://mycompany.quickbase.com/db/main. The realm is mycompany.
*   **$hours** is how long your api ticket will last for. I personally do not set this field, I only want the ticket to last my session.

So now we are connected to Quickbase! What’s next? Well, we need to connect to a table so we can start requesting data.

## Connecting to a Table

I mentioned earlier that I don’t like to select my database id in the constructor. This is because I may be accessing many tables later on and for consistency I like to set the db_id via the class property.

Personally, I like to set up an array of table id’s so I can easily change my tables:

```php 
$myQbTables = array(
 'table1' => 'dbId',
 'table2' => 'dbId',
 'table3' => 'dbId'
);
```

Now I can set the db_id property of my Quickbase object, **$qb**:

```php 
$qb->db_id = $myQbTables['table1'];
```

## Query Parameters

Once connected to the table you wish to query, you can set up your query parameters.

```php 
$queries = array(
  array(
    'fid'  => '15',
    'ev'   => 'EX',
    'cri'  => 'somevalue'
  )
);
```

The PHP Quickbase API expects an array of associative arrays to build the query as shown above. In this example we are looking for all records where field id (FID) 15 is evaluated EXACTLY like our criteria, ‘somevalue’. The 3 parameters are always required. There are many evaluation methods, you can find them listed [here](http://www.quickbase.com/api-guide/index.html#do_query.html#queryOperators).

To review: we are looking for all records that have an EXACT MATCH of ‘somevalue’ on FID 15 in our selected table.

Sometimes you may have multiple criteria for your query such as an AND or OR condition:

```php 
$queries = array(
  array(
    'fid'  => '15',
    'ev'   => 'EX',
    'cri'  => 'somevalue'
  ),
  array(
    'ao'  => 'AND',//OR is also acceptable
    'fid' => '16',
    'ev'  => 'EX',
    'cri' => true
  )
);
```

All you need to do is add another associative array to the **$queries** array. The key difference is each proceeding array must have the ‘ao’ parameter. This stands for **and/or**. You need to provide the operator you wish to use and then continue with your query parameters. This example is looking for ‘somevalue’ in FID 15 **AND** FID 16 must be true.

## Execute the Query

```php 
$queries = array(
  array(
    'fid'  => '15',
    'ev'   => 'EX',
    'cri'  => 'somevalue'
  ),
  array(
    'ao'  => 'AND',//OR is also acceptable
    'fid' => '16',
    'ev'  => 'EX',
    'cri' => true
  )
);

$results = $qb->do_query($queries, '', '', '3.4.5.6.7', '4', 'structured', 'sortorder-A');
```

#### Always select FID #3 in your $clist.

I highly recommend always requesting fid 3 in your clist. FID 3 will always be the Quickbase record id.

We store all the data we are getting back from Quickbase in a **$results** variable. This variable is actually an object with quite a bit of data, but we will dig into that a bit later. First, let’s discuss the arguments of do_query.

<pre>function do_query($queries = 0, $qid = 0, $qname = 0, $clist = 0, $slist = 0, $fmt ='structured', $options = "") {...}</pre>

**$queries** is our array variable we made earlier. You can leave the next two parameters blank as we wont need them. The **$clist** parameter is very important. It is a period delimited list of the field id’s we want returned to us. If we want all fields, simply provide ‘a’ to the **$clist**, but if we only want specific Fields to come back, we would provide it something like: ‘3.4.5.6’. **$slist** is very similar to $clist except that it is the fields that the records we are requesting will be sorted by. You can provide it a period delimited list as well. You can leave the $fmt as ‘structured’. Finally the $options. $options is a period delimited list of well, options. You can refer to [the api guide](http://www.quickbase.com/api-guide/index.html#gen_results_table.html#Request_Parameters) for the available options. Please note that if you are using the **$slist** parameter, you need to specify the direction you wish to sort for each **$slist** FID in this parameter.

```php 
$queries = array(
  array(
    'fid'  => '15',
    'ev'   => 'EX',
    'cri'  => 'somevalue'
  )
);

$results = $quickbase->do_query($queries, '', '', '5.6.7', '5', 'structured', 'sortorder-A**'**);
```

## Accessing Your Queried Data

We have queried Quickbase and now we are ready to do some work with the data. How do we get to it? We stored it in our **$results** variable.

```php 
foreach($results->table->records->record as $record) {
  $myVar1 = (string)$record->f[0];
  $myVar2 = (int)$record->f[1];
  $myVar3 = (string)$record->f[2];
  ...
}
```

**$results** is actually an object with quite a bit of information. You can see all the properties of the object by **var_dumping($results)**; Above is the basic structure you will need to use. I am [casting](http://php.net/manual/en/language.types.type-juggling.php) my values as well so I don’t get the XML attribute values, and only the record values I want. Ensure you know what data type each field is. You will also notice that **f[]** is an array. The array index refers to the order of the FID’s in your query’s **$clist**. From the query example above **$record->f[0]** is pointing to FID ‘5’, **$record->f[1]** is pointing to FID ‘6’ etc.

No matter what function you are calling: **store it in variable.** The object that gets returned to you has valuable information, including error info. This is essential for debugging!

## Error Handling

```php 
$queries = array(
  array(
    'fid'  => '15',
    'ev'   => 'EX',
    'cri'  => 'somevalue'
  )
);

$results = $quickbase->do_query($queries, '', '', '5.6.7', '5', 'structured', 'sortorder-A**'**);

if($results->errCode !== 0) {
  echo $results->errTxt;
}
```

Most quickbase api functions will return errCode and errText values in the object. An easy way to check if there are errors, is to see if the **errCode** is not 0\. If it is anything but 0, you have a problem. For more info, look at the **errTxt** property.

So, now you know the basics. Here are the rest of the basic functions for you with examples.

## Adding a Record to Quickbase

```php 
$recordVals = array(
  array(
    'fid'   => '',
    'value' => ''
  )
);

$results = $qb->add_record($recordVals);

$rid = $results->rid;
```

I recommend storing the rid so you can manipulate the record later.

## Editing a Record

edit_record() takes two arguments. The id of the record you want to edit, and the values that you want to change in the standard array() format.

```php 
$recordVals = array(
  array(
    'fid'   => '',
    'value' => ''
  )
);

$results = $qb->edit_record($rid, $recordVals);
```

## Deleting a Record

delete_record() only needs to id of the record you wish to delete.

```php 
$results = $qb->delete_record($rid);
```

For more info: [http://www.quickbase.com/api-guide/index.html](http://www.quickbase.com/api-guide/index.html)


</div>