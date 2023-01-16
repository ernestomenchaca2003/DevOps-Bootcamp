# Return and handle an error

Handling errors is an essential feature of solid code. In this section, you'll add a bit of code to return an error from the greetings module, then handle it in the caller.

> Note: This topic is part of the previous section that begins with [Create a Go module](../02-modules/).

This section has a copy of the previous section so you don't need to back.

## Handling errors:

1. In greetings/greetings.go, add the code highlighted below.

```
package greetings

import (
    "errors"
    "fmt"
)

// Hello returns a greeting for the named person.
func Hello(name string) (string, error) {
    // If no name was given, return an error with a message.
    if name == "" {
        return "", errors.New("empty name")
    }

    // If a name was received, return a value that embeds the name
    // in a greeting message.
    message := fmt.Sprintf("Hi, %v. Welcome!", name)
   return message, nil
}
```

