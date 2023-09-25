# No Click Email Tracking


This code will trigger a php file on a server you control from an email that you send, without the email recipient clicking any links. My proof of concept code bellow simply logs all the information in the $_SERVER variable to a logfile.txt. But this could also trigger any PHP code you like. however, the damage may be limited as most email providers do not allow client side scripting, like javascript, So you are limited to serverside code.
<br>

**Side effects/Benefits**<br>
This has the side effect that if the email is forwarded onto anyone else, the php code will be triggered also. so you can see the chain of custody that the email went through and a log of all the ip addresses that the email was opened on.


**Downfalls/Mitigations**<br>
It seems that google is somewhat wise to this technique and implements something called **GoogleImageProxy** that sends all external image requests through an anonymizing proxy.

You can tell if the traffic is being proxied by looking at the UESR_AGENT and looking for something like this: 
```
(via ggpht.com GoogleImageProxy)
```


**Test Setup** <br>
- Apache web Server
- PHP
- Outlook for sending the html embedded email

---

### Server code setup (image.php)
Upload an image to the same folder as the php file and call it image.png
```
<?php

// Token Code

$logFilePath = "logfile.txt";
$logMessage = "SERVER Information: " . var_export($_SERVER, true) . "\n";
file_put_contents($logFilePath, $logMessage, FILE_APPEND);


// Image Code

header("Content-type: image/jpeg");
$remoteImageURL = 'image.png';
$imageData = file_get_contents($remoteImageURL);
echo $imageData;

?>
```

---

### Email code setup
In your email add the following html code to the email html source:

```
<body>
<img src="http://myserver.com/image.php">
</body>
```
---

### Obfuscation & Token
You can make the link look more realistic and also make it a bit more customisable by adding a dummy get request to the php url.

This will appear in your log, but it will not actually change the functionality.

```
<body>
<img src="http://myserver.com/image.php?image=image.png">
</body>
```



---

### Logfile.txt
the log file will look something like this.

```
SERVER info: array (
  'HTTPS' => 'on',
  'UNIQUE_ID' => '[THE UNIQUE ID IS HERE]',
  'HTTP_X_PORT' => '25346',
  'HTTP_X_REAL_IP' => '###.###.###.###',
  'HTTP_X_FORWARDED_PROTO' => 'https',
  'HTTP_HOST' => 'myserver.com',
  'HTTP_X_ACCEPT_ENCODING' => 'gzip, deflate, br',
  'HTTP_CONNECTION' => 'close',
  'HTTP_SEC_CH_UA' => '"Chromium";v="116", "Not)A;Brand";v="24", "Google Chrome";v="116"',
  'HTTP_SEC_CH_UA_MOBILE' => '?0',
  'HTTP_SEC_CH_UA_PLATFORM' => '"Windows"',
  'HTTP_UPGRADE_INSECURE_REQUESTS' => '1',
  'HTTP_USER_AGENT' => 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.0.0 Safari/537.36',
  'HTTP_ACCEPT' => 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7',
  'HTTP_SEC_FETCH_SITE' => 'none',
  'HTTP_SEC_FETCH_MODE' => 'navigate',
  'HTTP_SEC_FETCH_USER' => '?1',
  'HTTP_SEC_FETCH_DEST' => 'document',
  'HTTP_ACCEPT_LANGUAGE' => 'en,en-AU;q=0.9,hu;q=0.8',
  'HTTP_COOKIE' => '[cookies are here],
  'PATH' => '/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/sbin:/sbin',
  'SERVER_SIGNATURE' => '',
  'SERVER_SOFTWARE' => 'Apache',
  'SERVER_NAME' => 'myserver.com',
  'SERVER_ADDR' => '###.###.###.###',
  'SERVER_PORT' => '443',
  'REMOTE_ADDR' => '###.###.###.###',
  'DOCUMENT_ROOT' => '/home/customer/www/myserver.com/public_html',
  'REQUEST_SCHEME' => 'https',
  'CONTEXT_PREFIX' => '',
  'CONTEXT_DOCUMENT_ROOT' => '/home/customer/www/myserver.com/public_html',
  'SERVER_ADMIN' => '[no address given]',
  'SCRIPT_FILENAME' => '/home/customer/www/myserver.com/public_html/image.php',
  'REMOTE_PORT' => '12136',
  'GATEWAY_INTERFACE' => 'CGI/1.1',
  'SERVER_PROTOCOL' => 'HTTP/1.0',
  'REQUEST_METHOD' => 'GET',
  'QUERY_STRING' => 'image=image.png',
  'REQUEST_URI' => '/image.php?image=image.png',
  'SCRIPT_NAME' => 'image.php',
  'PHP_SELF' => '/image.php',
  'REQUEST_TIME_FLOAT' => 1695597458.03009796142578125,
  'REQUEST_TIME' => 1695597458,
)
```
