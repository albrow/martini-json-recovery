JSON Recovery middleware for negroni
-------------------------------------

[Martini](https://github.com/go-martini/martini) is a framework for quickly writing modular web
applications/services in Go.

This recovery middleware catches any panics and wraps them up into a json response, including
the line number where the panic occured. Borrows heavily from the default
[recovery middleware in martini](https://github.com/go-martini/martini/blob/master/recovery.go).

See also https://github.com/go-martini/martini/blob/master/LICENSE

NOTE: this middleware relies on martini.Env being set. In production mode, it will render a
generic error message and exclude the line number for security reasons.

Usage
-----

### Installation

Installation is the same as any other package
```
go get github.com/albrow/martini-json-recovery
```

Make sure you have imported the package:

```go
import "github.com/albrow/martini-json-recovery"
```

Then add to the middleware stack:

```go
m.Use(recovery.JSONRecovery())
```

### Full Example

```go
package main

import (
	"github.com/albrow/martini-json-recovery"
	"github.com/go-martini/martini"
)

func main() {
	m := martini.Classic()
	m.Use(recovery.JSONRecovery())
	m.Get("/", func() string {
		panic("Oh no! Something went wrong")
	})
	m.Run()
}
```

The above code will render the following response:

```json
{
    "Code": 500,
    "Short": "internalError",
    "Errors": [
        "Oh no! Something went wrong"
    ],
    "From": "/Users/alex/programming/go/src/github.com/albrow/testing/martini-panic/server.go:12"
}
```

You can also inspect console for a more detailed message and full stack trace.