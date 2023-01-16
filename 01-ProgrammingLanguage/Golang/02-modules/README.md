# Create a Go module

## You'll create two modules. 

1. The first is a library which is intended to be imported by other libraries or applications. 
2. The second is a caller application which will use the first.

## This tutorial's sequence.

1. Create a module -- Write a small module with functions you can call from another module.
2. Call your code from another module -- Import and use your new module.
3. Return and handle an error -- Add simple error handling.
4. Return a random greeting -- Handle data in slices (Go's dynamically-sized arrays).
5. Return greetings for multiple people -- Store key/value pairs in a map.
6. Add a test -- Use Go's built-in unit testing features to test your code.
7. Compile and install the application -- Compile and install your code locally.

### Prerequisites
- Some programming experience. The code here is pretty simple, but it helps to know something about functions, loops, and arrays.
- A tool to edit your code. Any text editor you have will work fine. Most text editors have good support for Go. The most popular are VSCode (free), GoLand (paid), and Vim (free).
- A command terminal. Go works well using any terminal on Linux and Mac, and on PowerShell or cmd in Windows.

#### Start a module that others can use
Start by creating a Go module. In a module, you collect one or more related packages for a discrete and useful set of functions. 

Go code is grouped into packages, and packages are grouped into modules. 
Your module specifies dependencies needed to run your code, including the Go version and the set of other modules it requires.

As you add or improve functionality in your module, you publish new versions of the module. Developers writing code that calls functions in your module can import the module's updated packages and test with the new version before putting it into production use.

Open a command prompt and cd to your home directory.
**On Linux or Mac:**

`cd`

**On Windows:**

`cd %HOMEPATH%`

Create a `greetings` directory for your Go module source code.

From your home directory use the following commands:

```
mkdir greetings
cd greetings
```

Start your module using the `go mod init` command.
Run the `go mod init` command, giving it your module path -- here, use example.com/greetings. 
If you publish a module, this must be a path from which your module can be downloaded by Go tools. That would be your code's repository.


```
go mod init example.com/greetings
go: creating new go.mod: module example.com/greetings
```

The go mod init command creates a `go.mod` file to track your code's dependencies. So far, the file includes only the name of your module and the Go version your code supports. But as you add dependencies, the `go.mod` file will list the versions your code depends on. This keeps builds reproducible and gives you direct control over which module versions to use.

In your text editor, create a file in which to write your code and call it `greetings.go`.

Paste the following code into your `greetings.go` file and save the file.

```
package greetings

import "fmt"

// Hello returns a greeting for the named person.
func Hello(name string) string {
    // Return a greeting that embeds the name in a message.
    message := fmt.Sprintf("Hi, %v. Welcome!", name)
    return message
}
```

It returns a greeting to any caller that asks for one. You'll write code that calls this function in the next step.

In this code, you:

- Declare a greetings package to collect related functions.
- Implement a Hello function to return the greeting.
- This function takes a name parameter whose type is string. The function also returns a string. In Go, a function whose name starts with a capital letter can be called by a function not in the same package. This is known in Go as an exported name.

Declare a message variable to hold your greeting.

> In Go, the `:=` operator is a shortcut for declaring and initializing a variable in one line.

The first argument is a format string, and `Sprintf` substitutes the name parameter's value for the `%v` format verb. Inserting the value of the name parameter completes the greeting text.
Return the formatted greeting text to the caller.

# Call your code from another module

You've created a `greetings` module. 
Now you'll write code to make calls to the `Hello` function in the module you just wrote. You'll write code you can execute as an application, and which calls code in the greetings module.

Create a `hello` directory for your Go module source code. This is where you'll write your caller.

After you create this directory, you should have both a hello and a greetings directory at the same level in the hierarchy, like so:

<home>/
 |-- greetings/
 |-- hello/

```
cd ..
mkdir hello
cd hello
```

Enable dependency tracking for the code you're about to write.
To enable dependency tracking for your code, run the `go mod init` command, giving it the name of the module your code will be in.

```
go mod init example.com/hello
go: creating new go.mod: module example.com/hello
```

In your text editor, in the hello directory, create a file in which to write your code and call it `hello.go`.
Write code to call the `Hello` function, then print the function's return value.
To do that, paste the following code into `hello.go`.

```
package main

import (
    "fmt"

    "example.com/greetings"
)

func main() {
    // Get a greeting message and print it.
    message := greetings.Hello("Gladys")
    fmt.Println(message)
}
```

### In this code, you:

- Declare a main package. In Go, code executed as an application **must be in a main package.**
- Import two packages: `example.com/greetings` and the `fmt` package. This gives your code access to functions in those packages. Importing example.com/greetings (the package contained in the module you created earlier) gives you access to the Hello function. You also import fmt, with functions for handling input and output text (such as printing text to the console).
- Get a greeting by calling the greetings package’s `Hello` function.
- Edit the `example.com/hello` module to use your local `example.com/greetings` module.

For production use, you’d publish the `example.com/greetings` module from its repository (with a module path that reflected its published location), where Go tools could find it to download it. For now, because you haven't published the module yet, you need to adapt the `example.com/hello` module so it can find the `example.com/greetings` code on your local file system.

To do that, use the `go mod edit` command to edit the `example.com/hello` module to redirect Go tools from its module path (where the module isn't) to the local directory (where it is).

From the command prompt in the `hello directory`, run the following command:
`go mod edit -replace example.com/greetings=../greetings`

The command specifies that `example.com/greetings` should be replaced with `../greetings` for the purpose of locating the dependency. After you run the command, the `go.mod` file in the hello directory should include a replace directive:

```
module example.com/hello

go 1.16

replace example.com/greetings => ../greetings
```

From the command prompt in the `hello directory`, run the `go mod tidy` command to synchronize the example.com/hello module's dependencies, adding those required by the code, but not yet tracked in the module.

```
go mod tidy
go: found example.com/greetings in example.com/greetings v0.0.0-00010101000000-000000000000
```

After the command completes, the example.com/hello module's go.mod file should look like this:

```
module example.com/hello

go 1.16

replace example.com/greetings => ../greetings

require example.com/greetings v0.0.0-00010101000000-000000000000
```

The command found the local code in the `greetings directory`, then added a require directive to specify that `example.com/hello` requires `example.com/greetings`. You created this dependency when you imported the greetings package in `hello.go`.

At the command prompt in the `hello directory`, run your code to confirm that it works.
```
go run .
Hi, Gladys. Welcome!
```

Next steps: [Error handling](../03-error-handling)