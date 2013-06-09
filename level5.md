### Level 5

Open the .pcap file in Wireshark. In the list, select the one that does a http post request. 
Looks like this `POST /us-to-BELONG-are-data-your/all.php HTTP/1.1`

There is a binary file that has been sent here and we may extract it.

In the middle frame in Wireshark, expand the MIME Multipart list and find "Media Type: application/octet-stream (257 bytes)". Right click on it and hit "export selected packet bytes". I called mine lvl5.bin.

Now we got the file, but it isn't recognized as anything. Let's try to xor it.

To get the hex values like the format in level 3, we can convert the binary to hex and store the result in a file with a Linux shell:

`$ cat lvl5.bin | xxd -g 1 | cut -c10-58 | tr -d '\n' | sed 's/  / /g' > lvl5.hex`

And now for the xor. I also log what values I xor with.
```php
<?php

$x = file_get_contents("lvl5.hex");

for ($i = 66; $i < 67; $i++) { 
    echo "XOR ", $i, ": ";
    foreach (explode(" ", $x) as $v) {
        echo chr(hexdec($v) ^ $i);
    }
    print "\n";
}

?>
```

In between all the trash we find something like this:
`XOR 66: Rar!utfordring.txt`

If we xor with 66, it looks like we get a RAR-file.

```php
<?php

$x = file_get_contents("lvl5.hex");

foreach (explode(" ", $x) as $v) {
    echo chr(hexdec($v) ^ 66);
}

?>
```

Run the php script and pipe the result to a new file. E.g: "lvl5.rar":
`$ php lvl5.php > lvl5.rar`

When we open the file, we notice that it is password protected.

I just tried "BELONG" as the password because of the hint in the file path in the http request "/us-to-BELONG-are-data-your/all.php" and it worked.

The contents of utfordring.txt:
```
VmFyIGRldHRlIGZvciBsZXR0PyBWaWwgZHUgaGEgZmxlcmUgdXRmb3JkcmluZ2VyPyBTw7hrIHDDpSBlbiBzdGlsbGluZyBob3MgTlNNLiBLYW5za2plIHN0aWxsaW5nZW4gTmV0dmVya3NhbmFseXNlIGVyIG5vZSBmb3IgZGVnPwo=
```

Let's try base64_decode on that string:
```php
<?php

$x = "VmFyIGRldHRlIGZvciBsZXR0PyBWaWwgZHUgaGEgZmxlcmUgdXRmb3JkcmluZ2VyPyBTw7hrIHDDpSBlbiBzdGlsbGluZyBob3MgTlNNLiBLYW5za2plIHN0aWxsaW5nZW4gTmV0dmVya3NhbmFseXNlIGVyIG5vZSBmb3IgZGVnPwo=";

echo base64_decode($x);

?>
```

Result:
```
Var dette for lett? Vil du ha flere utfordringer? Søk på en stilling hos NSM. Kanskje stillingen Netverksanalyse er noe for deg?
```
