# Making GPS output copy-and-pastable into Google Maps

Ugly one-liner to format a string.

Example:
```
$GPGGA,232002.00,3502.12642,N,11041.48589,W,2,12,0.53,1476.0,M,-24.5,M,,0000*59
```

```
  grep GPGGA gps.txt | sed -ne 's/\(.*\)\([0-9]\{2,3\}\)\([0-9]\{2\}\)\.\([0-9]\{2\}\)\([0-9]\{3\}\)...\([0-9]\{2,3\}\)\([0-9]\{2\}\)\.\([0-9]\{2\}\)\([0-9]\{3\}\)\(.*\)/\2 \3\.\4N\, \6 \7\.\8W/p' | uniq > gps_clean.txt
```

