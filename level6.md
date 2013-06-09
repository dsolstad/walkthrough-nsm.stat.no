### Level 6

First we can try to insert the hex values into a file:
<?php

$x = "31 db f7 e3 68 ff f4 f5 e2 68 fb f5 b0 f8 68 b0 fb fc ff 68 fc f5 e2 f5 68 f5 e2 b0 f6 68 e2 f5 fe f7 68 c6 f9 b0 e4 b9 90 90 90 90 31 0c 04 04 04 3c 1c 75 f7 89 e1 31 c0 b0 04 b2 1c cd 80 b0 01 cd 80";

foreach (explode(" ", $x) as $v) {
    echo chr(hexdec($v));
}

?>

Run the script and pipe it to lvl6.bin:
`$ php lvl6.php > lvl6.bin`

gdb doesn't want to touch it, so after disassembling the file with [Ida](https://www.hex-rays.com/products/ida/support/download_freeware.shtml "Ida") I get this:

```asm
public start
start proc near
xor     bx, bx
mul     bx
push    0F4FFh
cmc
loop    near ptr 172h
sti
cmc
mov     al, 0F8h
push    0FBB0h
cld
jmp     dword ptr [bx+si-4]
start endp
```

That code make no sense to me.
Let's try to xor with different values and store each result in a new file.

```php
<?php

$x = "31 db f7 e3 68 ff f4 f5 e2 68 fb f5 b0 f8 68 b0 fb fc ff 68 fc f5 e2 f5 68 f5 e2 b0 f6 68 e2 f5 fe f7 68 c6 f9 b0 e4 b9 90 90 90 90 31 0c 04 04 04 3c 1c 75 f7 89 e1 31 c0 b0 04 b2 1c cd 80 b0 01 cd 80";

for ($i = 0; $i < 256; $i++) {
    $output = '';
    foreach (explode(" ", $x) as $v) {
        $output .= chr(hexdec($v) ^ $i);
    }
    file_put_contents("lvl6/lvl6_".$i.".bin", $output);
}

?>
```

Now we can check if any of the generated files are recognized as a known file format:
```
~/lvl6$ for file in *; do file $file; done | less | grep -v data$ | grep -v Non-ISO
lvl6_137.bin: COM executable for DOS
lvl6_155.bin: Dyalog APL
lvl6_177.bin: 8086 relocatable (Microsoft)
lvl6_189.bin: DOS executable (COM)
lvl6_193.bin: SysEx File - ART
lvl6_216.bin: DOS executable (COM)
lvl6_218.bin: DOS executable (COM)
lvl6_23.bin: Sendmail frozen configuration  - version \354\353\350\177\353\342\365\342\177\342\365\247\341\177\365\342\351\340\177\321\356\247\363\256\207\207\207\207&\033\023\023\023+\013b\340\236\366&\327\247\023\245\013\332\227\247\026\332\227
lvl6_50.bin: DBase 3 data file

```

Now we got some interesting results. Let's start with the DBase file.

$ dbview lvl6_50.bin
File version  : 3
Last update   : 197/209/2133
Number of recs: -943272614
Header length : 23248
Record length : 51145

Seems like a corrupt dBase 3 file.

After trying to disassemble the binary DOS files (with ) I am now lost. Seems like all the files are broken.
