# ipset

netlink ipset package for Go.

## Usage

### Your code:
```Go
package main

import (
	"log"

	"github.com/cuisj/ipset"
)

func main() {
	// must call Init first
	if err := ipset.Init(); err != nil {
		log.Printf("error in ipset Init: %s", err)
		return
	}

	// default is ipv4 without timeout
	ipset.Destroy("myset")
	ipset.Create("myset")
	ipset.Add("myset", "1.1.1.1")
	ipset.Add("myset", "2.2.2.0/24")
	
	// ipv4 timeout, do not create if it exists
	ipset.Add("myset", "3.3.3.4", ipset.OptTimeout(60), ipset.OptExcl())

	// ipv6 and timeout example
	// ipset create myset6 hash:net family inet6 timeout 60
	ipset.Create("myset6", ipset.OptIPv6(), ipset.OptTimeout(60))
	ipset.Flush("myset6")

	ipset.Add("myset6", "2022::1", ipset.OptTimeout(10))
	ipset.Add("myset6", "2022::1/32")
}
```

### Result:
`ipset list myset`

```
Name: myset
Type: hash:net
Revision: 1
Header: family inet hashsize 1024 maxelem 65536
Size in memory: 552
References: 0
Number of entries: 2
Members:
1.1.1.1
2.2.2.0/24
3.3.3.4
```

`ipset list myset6`

```
Name: myset6
Type: hash:net
Revision: 1
Header: family inet6 hashsize 1024 maxelem 65536 timeout 60
Size in memory: 1432
References: 0
Number of entries: 2
Members:
2022::1 timeout 9
2022::/32 timeout 59
```
