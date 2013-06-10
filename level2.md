### Level 2

ULFC://TXBYS.AKY.FLMG.FA/GSD/QM/GGXAEVDVFSRF?

This also looks like rot 13, but more complex.

We can easiliy guess that the first part should be HTTP://BLOGG.NSM.STAT.NO, but what about the rest?

I wrote this script that finds the difference in the number of characters between the encrypted and the "guessed" characters I just made.

```php
<?php

$x = "H = U
T = L
T = F
P = C
B = T
L = X
O = B
G = Y
G = S
N = A
S = K
M = Y
S = F
T = L
A = M
T = G
N = F
O = A";

foreach (explode("\n", $x) as $line) {
    $chr = explode(" = ", $line);
    echo $a=ord($chr[0]),", ",$b=ord($chr[1])," diff= ",$b-$a,"\n";
}

?>
```

Result:
```
72, 85 diff= 13
84, 76 diff= -8
84, 70 diff= -14
80, 67 diff= -13
66, 84 diff= 18
76, 88 diff= 12
79, 66 diff= -13
71, 89 diff= 18
71, 83 diff= 12
78, 65 diff= -13
83, 75 diff= -8
77, 89 diff= 12
83, 70 diff= -13
84, 76 diff= -8
65, 77 diff= 12
84, 71 diff= -13
78, 70 diff= -8
79, 65 diff= -14
```

We can see a pattern here. First char rot 13, then rot 8 and then 14, before it starts over again.
Now we can follow the same pattern and convert the rest.

```php
<?php

function str_rot($s, $n = 13) { 
    $n = (int)$n % 26; 
    if (!$n) return $s; 
    for ($i = 0, $l = strlen($s); $i < $l; $i++) { 
        $c = ord($s[$i]); 
        if ($c >= 97 && $c <= 122) { 
            $s[$i] = chr(($c - 71 + $n) % 26 + 97); 
        } else if ($c >= 65 && $c <= 90) { 
            $s[$i] = chr(($c - 39 + $n) % 26 + 65); 
        } 
    } 
    return $s; 
}

$str = "ULFCTXBYSAKYFLMGFAGSDQMGGXAEVDVFSRF";
$i = 0;
foreach (str_split($str) as $v) {
    if (($i % 3) == 0) echo str_rot($v, 13);
    elseif (($i % 3) == 1) echo str_rot($v, 8);
    elseif (($i % 3) == 2) echo str_rot($v, 14);
    $i++;
}

?>
```

Result:
`HTTPBLOGGNSMSTATNOTARDUUTFORDRINGEN`

That is:
`HTTP://BLOGG.NSM.STAT.NO/TAR/DU/UTFORDRINGEN`