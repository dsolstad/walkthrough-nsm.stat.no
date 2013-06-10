### Level 6

First we can try to insert the hex values into a file:

```php
<?php

$x = "31 db f7 e3 68 ff f4 f5 e2 68 fb f5 b0 f8 68 b0 fb fc ff 68 fc f5 e2 f5 68 f5 e2 b0 f6 68 e2 f5 fe f7 68 c6 f9 b0 e4 b9 90 90 90 90 31 0c 04 04 04 3c 1c 75 f7 89 e1 31 c0 b0 04 b2 1c cd 80 b0 01 cd 80";

foreach (explode(" ", $x) as $v) {
    echo chr(hexdec($v));
}

?>
```

Run the script and pipe the output to lvl6.bin:
`$ php lvl6.php > lvl6.bin`

Now we can see what kind of file this is:
```
$ file lvl6.bin
lvl6.bin: data
```

gdb and objdump didn't want to touch, but I found this online disassembler useful: `http://onlinedisassembler.com/odaweb/run_hex`

Just paste the hex code in there and you will get this output where I have commented each line.

```asm
 xor    ebx,ebx             ; XOR the value in ebx with ebx (make ebx zero)
 mul    ebx                 ; Dunno why this is here
 push   0xe2f5f4ff          ; Pushes 0xe2f5f4ff to stack (4 bytes)
 push   0xf8b0f5fb          ; ...
 push   0xfffcfbb0          ; ...
 push   0xf5e2f5fc          ; ...
 push   0xf6b0e2f5          ; ...
 push   0xf7fef5e2          ; ...
 push   0xe4b0f9c6          ; ...
 mov    ecx,0x90909090      ; Pushes 0x90909090 to the ecx register
 loc_0000002c:                    ; Start of a loop
 xor    DWORD PTR [esp+eax*1],ecx ; XOR first 4 bytes (first item) on stack with value in ecx
 add    al,0x4                    ; Adds 4 (next item) to the register eax
 cmp    al,0x1c                   ; Compare eax to 28. If eax is not equal to 28, continue loop
 jne    loc_0000002c              ;   i.e: Loop 28/4=7 times for each item on the stack
 mov    ecx,esp                   ; Moves the addr of stack to ecx. (What sys_write will write)
 xor    eax,eax                   ; XOR the value in eax with eax (make eax zero)
 mov    al,0x4                    ; Adds 4 to eax. (Used for int 0x80 to call sys_write)
 mov    dl,0x1c                   ; Adds 28 to edx. (Numb of bytes sys_write will output)
 int    0x80                      ; Linux kernel interrupt. (Calls sys_write)
 mov    al,0x1                    ; Adds 1 to eax. (Used for int 0x80 to call sys_exit)
 int    0x80                      ; Linux kernel interrupt. (Calls sys_exit)
```

What the program basically does is to XOR the values on the stack with 0x90909090 and then output the new values.
Here I have translated the assembly to php:

```php
<?php

// The stack with the pushed values.
$stack = array(0xe2f5f4ff, 0xf8b0f5fb, 0xfffcfbb0, 0xf5e2f5fc, 
               0xf6b0e2f5, 0xf7fef5e2, 0xe4b0f9c6);

// We need to reverse the array, because a stack is last in first out.
$stack = array_reverse($stack);

// The value we are going to XOR with.
$ecx = 0x90909090;

// Iterate through the array and replace the current stack with a XORed one.
for ($i = 0; $i < 7; $i++) {
    $stack[$i] = dechex($stack[$i] ^ $ecx);
}

// Iterate through the stack.
for ($i = 0; $i < 7; $i++) {
    // Split up the values in pairs.
    $hex = str_split($stack[$i], 2);
    // Now we convert the hex to ascii chars before we ouput it.
    for ($j = 4; $j >= 0; $j--) {
        echo chr(hexdec($hex[$j]));
    }
}

?>
```

Result: `Vi trenger flere kloke hoder`

English: `We need more bright minds`

Recommended reading:
http://asm.sourceforge.net/intro/hello.html