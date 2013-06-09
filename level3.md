### Level 3

Let's try and "xor" hex values with the combinations from 0 to 265 and see what we get.

```php
<?php

$x = "32 19 1C 12 1B 57 2F 38 25 57 04 18 1A 57 13 12 03 03 12 57 12 05 57 18 11 03 12 57 15 05 02 1C 03 57 16 01 57 1A 16 1B 00 16 05 12 59 57 3C 16 19 04 1C 1D 12 57 13 02 57 12 05 57 1E 19 03 12 05 12 04 04 12 05 03 57 1E 57 16 19 16 1B 0E 04 12 57 16 01 57 1A 16 1B 00 16 05 12 48";

for ($i = 0; $i < 256; $i++) { 
    foreach (explode(" ", $x) as $v) {
        echo chr(hexdec($v) ^ $i);
    }
    print "\n";
}

?>
```

Result:
```
...
@kn`i%]JW%vjh%a`qq`%`w%jcq`%gwpnq%ds%hdirdw`+%Ndkvno`%ap%`w%lkq`w`vv`wq%l%dkdi|v`%ds%hdirdw`:
Ajoah$\KV$wki$`appa$av$kbpa$fvqop$er$iehseva*$Oejwona$`q$av$mjpavawwavp$m$ejeh}wa$er$iehseva;
Fmhfo#[LQ#pln#gfwwf#fq#lewf#aqvhw#bu#nbotbqf-#Hbmphif#gv#fq#jmwfqfppfqw#j#bmbozpf#bu#nbotbqf<
Glign"ZMP"qmo"fgvvg"gp"mdvg"`pwiv"ct"ocnucpg,"Iclqihg"fw"gp"klvgpgqqgpv"k"clcn{qg"ct"ocnucpg=
Dojdm!YNS!rnl!eduud!ds!ngud!cstju!`w!l`mv`sd/!J`orjkd!et!ds!houdsdrrdsu!h!`o`mxrd!`w!l`mv`sd>
Enkel XOR som dette er ofte brukt av malware. Kanskje du er interessert i analyse av malware?
Jadjc/W@]/|`b/kj{{j/j}/`i{j/m}zd{/ny/bncxn}j!/Dna|dej/kz/j}/fa{j}j||j}{/f/nancv|j/ny/bncxn}j0
K`ekb.VA\.}ac.jkzzk.k|.ahzk.l|{ez.ox.cobyo|k .Eo`}edk.j{.k|.g`zk|k}}k|z.g.o`obw}k.ox.cobyo|k1
Hcfha-UB_-~b`-ihyyh-h-bkyh-oxfy-l{-`lazlh#-Flc~fgh-ix-h-dcyhh~~hy-d-lclat~h-l{-`lazlh2
Ibgi`,TC^,ca,hixxi,i~,cjxi,n~ygx,mz,am`{m~i",Gmbgfi,hy,i~,ebxi~ii~x,e,mbm`ui,mz,am`{m~i3
...
```

And there we have it. A sentance that makes sense!
`Enkel XOR som dette er ofte brukt av malware. Kanskje du er interessert i analyse av malware?`