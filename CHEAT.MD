## Quickbase Cheatsheet

### Visit the Quickbase API Reference Documentation
API DOCS [link](https://help.quickbase.com/api-guide/index.html)

## Convert the milliseconds that QB returns into human date
```php
$mil = 1227643821310;
$seconds = $mil / 1000;
echo date("d/m/Y H:i:s", $seconds);

```