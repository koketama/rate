# rate
*Rate is a distributed rate limiter, which relay on **golang.org/x/time/rate** ,and base on redis to records the summary of all events.*

**By default, rate every 300ms report/fetch increment to/from redis, the qps of one node is about 7 per second, it needs to consider if there are lots of nodes, redis will be the major factor.**





#### Example

```go
package main

import (
	"github.com/koketama/rate"
)

func main() {
	redis, err := rate.NewSingleRedis("127.0.0.1:6379", "", 0, nil)
	if err != nil {
		// TODO
	}

	conf := new(rate.Config)
	conf.Identifier = "dummy-test"
	conf.Limit.Upper = 1000
	conf.Limit.Lower = 100
	conf.Log.Enable = true

	limiter, err := rate.NewLimiter(conf, redis)
	if err != nil {
		// TODO
	}
	defer limiter.Close()

	limiter.Allow() // if exceed the limit, this will wait a while
	// do something

	limiter.UpdateRate(2000, 200) // update to new rate
}
```

