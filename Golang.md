According to the language specification, Go source code is always written in UTF-8.

# Go Coding tips documents

**Official Effective Go doc**

https://golang.org/doc/effective_go

Marketed as first reading for new Golang programmers.

**Uber**

https://github.com/uber-go/guide/blob/master/style.md

Contains best practice tips/snippets used to deal with Go's design choices (e.g. prioritizing low barrier to entry over solid language design).

**Other Golang guides**

https://github.com/golang/go/wiki/CommonMistakes
https://github.com/golang/go/wiki/CodeReviewComments

# Variables

Every type in Go is a value type. It’s just that sometimes the value (e.g. a map, slice) is a pointer.

**Naming**
Use camelCase (and never snake_case). DO NOT USE uppercase names for constants in Go - this is because Go uses the case of the first letter in the name of a package-level declaration to determine if the item is accessible outside the package.

Variables are by default auto-initialized to their zero values.

Go doesn’t allow automatic type promotion between variables. You must use type conversion when variable types do not match.

**Immutable declaration**
Go doesn’t provide a way to specify that a value calculated at runtime is immutable. 'Immutability' is limited to things that can only hold values that the compiler can figure out at compile time.

const x int64 = 10

**:=**
If you are declaring a variable at package level, you must use the var keyword because := is not legal outside of functions.

**Type Aliases**
In Go you can do this:
type Bar = Foo

**Type Assertion**
Go allows you to express an assertion about an identifier's type:
i2 := i.(string)

However, in case of failed type assertion it throws a panic (and you never want to panic in Go).

**Type Switch**
```go
switch j := i.(type) {
case nil:
  // i is nil, type of j is interface{}
case int:
  // j is of type int
case MyInt:
  // j is of type MyInt
case io.Reader:
  // j is of type io.Reader
case string:
  // j is a string
case bool, rune:
  // i is either a bool or rune, so j is of type interface{}
default:
  // no idea what i is, so j is of type interface{}
}
```

A Type Switch looks like a vanilla switch statement, but instead of specifying a boolean operation, you specify a variable of an interface type and follow it with .(type).

Since the purpose of a type switch is to derive a new variable from an existing one, it is idiomatic to assign the variable being switched on to a variable of the same name (i := i.(type)), making this one of the few places where shadowing is a good idea.

Use Type Switches sparringly. One common use of a type assertion is to see if the concrete type behind the interface also implements another interface.

## iota

Iota expression is like a numeric universal counter starting at 0 (think C-style enum).

An example:
```go
const (
    Monday Digit = iota
    Tuesday
    Wednesday
    Thursday
    Friday
)
fmt.Println(Wednesday)
fmt.Println(Thursday)
```

The above is equivalent to the next declaration of constants:
```go
const (
    Monday = 0
    Tuesday = 1
    Wednesday = 2
    Thursday = 3
    Friday = 4
)
```

# Simple Types

byte is an alias for uint8.
int is int32 or int64, depending on CPU arch. + there are uncommon 64bit CPUs (e.g. MIPS64) that still use int32 here.
There are two other special names for integer types, rune and uintptr.

**nil**
nil is defined in the universe block/scope.  Because nil is a value defined in the universe block, it can be shadowed. Hence, never name a variable or function nil!!

**Complex numbers**
complex64 uses float32 values to represent the real and imaginary part, and complex128 uses float64 values.

**String**
They are immutable. Go uses a sequence of bytes (and not runes) to represent a string.

**Single char**
Go calls this rune (I think). rune type is an alias for the int32 type.

# Composite Types

## Tuple

https://stackoverflow.com/questions/13670818/pair-tuple-data-type-in-go

Go has no support for the tuple type, which means that Go does not officially care about tuples, despite the fact that it has support for certain uses of tuples.

You can kind-of simulate them as a pair via e.g. arrays, or anonymous structs.

```go
// You can have a function return two values that you get in a single statement:
min, _ := strconv.ParseFloat(arguments[1], 64)

// fn returns a 3-tuple
func retThree(x int) (int, int, int) {
    return 2 * x, x * x, -x
}
n1, n2, n3 := retThree(20)
```

## Arrays

They are indexed from 0.

```go
var x [3]int // 3 element long array, with zero values
var x = [3]int{10, 20, 30}
var x = [...]int{10, 20, 30} // When using an array literal to initialize an array, you can leave off the number and use '...' instead
var x = [12]int{1, 5: 4, 6, 10: 100, 15}  // this creates [1, 0, 0, 0, 0, 4, 6, 0, 0, 0, 100, 15]
```

Go only has one-dimensional arrays, but you can simulate multidimensional arrays:
```go
var x [2][3]int // x is an array of length 2 whose type is an array of ints of length 3
```

An out-of-bounds read or write with a variable index compiles but fails at runtime with a panic - no negative indexes allowed either.

Go considers the size of the array to be part of the type of the array. This makes an array that’s declared to be [3]int a different type from an array that’s declared to be [4]int. This also means that you cannot use a variable to specify the size of an array, because types must be resolved at compile time, not at runtime. Also, you can’t use type conversion to convert arrays of different sizes to identical types.

Due to these restrictions, don’t use arrays unless you know the exact length you need ahead of time. Arrays exist in Go to provide the backing store for slices.

## Slices

A slice is implemented as a struct with three fields: an int field for length, an int field for capacity, and a pointer to a block of memory.

Empty slice is 'nil'. In Go, nil is an identifier that represents the lack of a value for some types.

You can create a slice using an empty slice literal:
```go
var x = []int{}
```

This creates a zero-length slice, which is non-nil (comparing it to nil returns false). Otherwise, a nil slice works identically to a zero-length slice. A situation where a zero-length slice is useful is when converting a slice to JSON.

## Dictionary/Map

The map type is written as map[keyType]valueType:
var nilMap map[string]int

We can use a := declaration to create a map variable by assigning it a (in this case empty) map literal:
NB An empty map literal is not the same as a nil-Map.
totalWins := map[string]int{}

A nonempty map literal:
```go
teams := map[string][]string {
  "Orcas": []string{"Fred", "Ralph", "Bijou"},
  "Lions": []string{"Sarah", "Peter", "Billie"},
  "Kittens": []string{"Waldo", "Raul", "Ze"},
}
```

**comma ok idiom**
This is Go's idiomatic way of dealing with potential failure values (i.e. it is Go's version of the the Maybe/Either monad).

A map returns the zero value if you ask for the value associated with a key that’s not in the map! If you need to find out if a key is in a map. Go provides the comma ok idiom to tell the difference between a key that’s associated with a zero value and a key that’s not in the map:
```go
m := map[string]int{
"hello": 5,
"world": 0,
}

v, ok := m["hello"]
```

With the comma ok idiom you assign the results of a map read to two variables. The first gets the value associated with the key. The second value (usually named ok) returned is a bool. If ok is true, the key is present in the map. If ok is false, the key is not present.

**Delete a value from Map**
delete(mymap, "hello")

## Set

**Go doesn’t include a set**, but you can use a map to simulate it. I.e. use the key of the map for the type that you want to put into the set and use a bool for the value.
```go
intSet := map[int]bool{}
vals := []int{5, 10, 2, 5, 8, 7, 3, 9, 1, 2, 10}
for _, v := range vals {
intSet[v] = true
}
```

Some people prefer to use struct{} for the value when a map is being used to implement a set. The advantage is that an empty struct uses zero bytes, while a boolean uses one byte. The disadvantage is that using a struct{} makes your code more clumsy. You have a less obvious assignment, and you need to use the comma ok idiom to check if a value is in the set:
```go
intSet := map[int]struct{}{}
vals := []int{5, 10, 2, 5, 8, 7, 3, 9, 1, 2, 10}
for _, v := range vals {
  intSet[v] = struct{}{}
}

if _, ok := intSet[5]; ok {
  fmt.Println("5 is in the set")
}
```

Unless you have very large sets, it is unlikely that the difference in memory usage is significant enough to outweigh the disadvantages.

## Structs

NB  Go doesn’t have classes, because it doesn’t have inheritance.

Struct declaration:
```go
type person struct {
    name string
    age int
    pet string
}
```

Once a struct type is declared, we can define variables of that type:
```go
var fred person
bob := person{} // here a struct literal is assigned to a variable
```

Unlike maps, there is no difference between assigning an empty struct literal and not assigning a value at all. Both initialize all of the fields in the struct to their zero values.

There are two different styles for a nonempty struct literal:
```go
// first style
julia := person{
    "Julia",
    40,
    "cat",
}

// second style
beth := person{
    age: 30,
    name: "Beth",
}
```

**Anonymous struct**
You don't have to declare the struct type first. You can just use an anonymous struct type:
```go
pet := struct {
    name string
    kind string
}{
    name: "Fido",
    kind: "dog",
}
```

These are used with (e.g. JSON, protocol buffers) unmarshaling and marshaling, and also when writing tests.

# for loop

Golang has no do/while loops. Instead, it uses the for loop in four different formats:

* A complete, C-style for: for i := 0; i < 10; i++ { ... }
* A condition-only for: for i < 100 { ... }
* An infinite for. for { ...break/continue }
* for-range. for index, value := range evenVals { ... }.

If you don’t need to access the key, use an underscore (_) as the variable’s name:
for _, v := range evenVals { ... }

If you want the key, but don’t want the value, Go allows you to just leave off the second variable:
for k := range uniqueNames { ... }

The most common reason for iterating over the key is when a map is being used as a set (and therefore, you don't care about the value).

**Nested for loops with labels**
You can add a label to the outer for loop so that continue applies to it:
```go
func main() {
  samples := []string{"hello", "apple_π!"}
outer:
  for _, sample := range samples {
    for i, r := range sample {
      fmt.Println(i, r, string(r))
      if r == 'l' {
        continue outer
      }
    }
    fmt.Println()
  }
}
```

# Functions

Go is a call by value language, meaning Go always makes a copy of the value of the variable (use pointers to avoid copy).

Go doesn’t have named and optional input parameters.

**Return Values**
Go has a return keyword for returning values from a function. If a function returns a value, you must supply a return. If a function returns nothing, a return statement is not needed at the end of the function. The return keyword is only needed in a function that returns nothing if you are exiting from the function before the last line.

Go allows for multiple return values (NB the returned value is not a tuple; these are individual values):
return 0, 0, errors.New("cannot divide by zero")

**Named Return Values**
```go
func divAndRemainder(numerator int, denominator int) (result int, remainder int, err error) {
  ...
  return result, remainder, err
}
```

If you only want to name some of the return values, you can do so by using _ as the name for any return values you want to remain nameless.

Just like any other variable, you can shadow a named return value. Be sure that you are assigning to the return value and not to a shadow of it.

**Never use Blank Returns with Named Return Values**
If you use named return values, you need to be aware of one severe misfeature in Go: blank (sometimes called naked) returns. If you have named return values, you can just write return without specifying the values that are returned. This returns the last values assigned to the named return values.

**Variadic functions**
Go supports variadic parameters. The variadic parameter must be the last (or only) parameter in the input parameter list. The variable that’s created within the function is a slice of the specified type.

```go
func varFunc(input ...string) {
    fmt.Println(input)
}
```

The input function argument is a slice and will be handled as a slice inside the varFunc() function. The ... operator used as ...Type is called the pack operator, whereas the unpack operator ends with ... and begins with a slice. A variadic function cannot use the pack operator more than once.

Here you can see another variadic function named oneByOne() that accepts a single string and a variable number of integer arguments. The s function argument is a slice:

```go
func oneByOne(message string, s ...int) int {
    fmt.Println(message)
    sum := 0
    for i, a := range s {
        fmt.Println(i, a)
        sum = sum + a
    }
    s[0] = -1000
    return sum
}
```

**Function Type**
```go
type opFuncType func(int,int) int
```

**Anonymous Functions**
You declare an anonymous function with the keyword func immediately followed by the input parameters, the return values, and the opening brace. It is a compile-time error to try to put a function name between func and the input parameters.

```go
func main() {
  for i := 0; i < 5; i++ {
    func(j int) {
      fmt.Println("printing", j, "from inside of an anonymous function")
    }(i)
  }
}
```

**defer**
In Go, the cleanup code is attached to the function with the defer keyword (think of it as a finally block). The code within defer closures runs after the return statement. You can supply a function with input parameters to a defer. Just as defer doesn’t run immediately, any variables passed into a deferred closure aren’t evaluated until the closure runs.

## Adding methods (i.e. functions) to functions

Just like you can add a method to an int or a string,  Go allows methods on any user-defined type, including user-defined function types. This allows functions to implement interfaces.

The most common usage is for HTTP handlers. An HTTP handler processes an HTTP server request. It’s defined by an interface:
```go
type Handler interface {
  ServeHTTP(http.ResponseWriter, *http.Request)
}
```

By using a type conversion to http.HandlerFunc, any function that has the signature func(http.ResponseWriter,*http.Request) can be used as an http.Handler:
```go
type HandlerFunc func(http.ResponseWriter, *http.Request)

func (f HandlerFunc) ServeHTTP(w http.ResponseWriter, r *http.Request)
```

## Higher-order Functions

**Function that accept a function as operand**

```go
func funFun(f func(int) int, v int) int {
    return f(v)
}
```

**Function that returns a function**

```go
func funReturnFun() func() int {
    i := 0
    return func() int {
        i++
        return i * i
  }
}
```

# Pointers

There are no C++ style pointer arithmetics in Go. However, the Go standard library does have an unsafe package that lets you do some low-level operations on data structures.

The zero value for a pointer is nil, which is also the zero value for slices, maps, and functions - all implemented with pointers (as are channels and interfaces).

**Reference/address operator**
```go
var x int32 = 10
pointerX := &x  // & is the C-style address operator
```

**Dereference**

Before dereferencing a pointer, you must make sure that the pointer is non-nil. Your program will panic otherwise.
```go
x := 10
pointerToX := &x
fmt.Println(pointerToX) // prints a memory address
fmt.Println(*pointerToX) // prints 10
```

**Pointer type**

A pointer type is a type that represents a pointer to a value with the specified type. It is written with a * before a type name. A pointer type can be based on any type:
```go
x := 10
var pointerToX *int
pointerToX = &x
```

# Intefaces

Go supports implicit interfaces. I.e. anything that looks like it implements an interface is assumed to implement it (think Scala 2.X implicits).

**Empty interface**
An empty interface type simply states that the variable can store any value whose type implements zero or more methods. This just happens to match every type in Go.

Sometimes in a statically typed language, you need a way to say that a variable could store a value of any type. Go uses interface{} to represent this.

One common use of the empty interface is as a placeholder for data of uncertain schema that’s read from an external source, like a JSON file:
```go
// one set of braces for the interface{} type,
// the other to instantiate an instance of the map
data := map[string]interface{}{}
```

Another use of interface{} is as a way to store a value in a user-created data structure. This is due to Go’s current lack of user-defined generics (generics seem to be available only for the compiler).

# Context

NB The context package existed as an external package first. It became a standard Go pkg in Go version 1.7.

This is something like ThreadLocal in other languages. The main purpose of the context package is to define the Context type and support cancellation. Context is often used to pass down common data that can be used by all downstream operations.

The Context type is an interface with four methods named Deadline(), Done(), Err(), and Value(). The good news is that you do not need to implement all of these functions of the Context interface – you just need to modify a Context variable using functions such as context.WithCancel(), context.WithDeadline(), and context.WithTimeout().

All three of these functions return a derived Context (the child) and a CancelFunc function. Calling the CancelFunc function removes the parent's reference to the child and stops any associated timers. This means that the Go garbage collector is free to garbage collect the child goroutines that no longer have associated parent goroutines.

**Context Cancellation**
This can be imagined as Go's Maybe/Either monad.

# Error Handling

error is a built-in interface that defines a single method:
```go
type error interface {
  Error() string
}
```

Anything that implements this interface is considered an error. The reason why we return nil from a function to indicate that no error occurred is that nil is the zero value for any interface type.

```go
type StatusErr struct {
  Status Status
  Message string
}

func (se StatusErr) Error() string {
  return se.Message
}

// use StatusErr as:
data, err := getData(file)
if err != nil {
  return nil, StatusErr{
    Status:  NotFound,
    Message: fmt.Sprintf("file %s not found", file),
  }
}
```

Even when you define your own custom error types, always use error as the return type for the error result. This allows you to return different types of errors from your function and allows callers of your function to choose not to depend on the specific error type. Either explicitly return nil when no error occurs or define the variable to be of type error.

Two functions in stdlib to create errors:

* errors.New("only even numbers are processed")
* fmt.Errorf("%d isn't an even number", i)

**Sentinel Errors**
Sentinel errors are usually used to indicate that you cannot start or continue processing.

They were introduced by Dave Cheney in this blogpost:
https://dave.cheney.net/2016/04/27/dont-just-check-errors-handle-them-gracefully

Sentinel errors are one of the few variables that are declared at the package level. By convention, their names start with Err (with the notable exception of io.EOF). They should be treated as read-only; there’s no way for the Go compiler to enforce this, but it is a programming error to change their value.

An example of sentinel error usage would be:
```go
_, err := zip.NewReader(notAZipFile, int64(len(data)))
```

**Wrapping Errors**
The standard library provides a function for unwrapping errors, the Unwrap function in the errors package. You pass it an error and it returns the wrapped error, if there is one. If there isn’t, it returns nil.

```go
func fileChecker(name string) error {
  f, err := os.Open(name)
  if err != nil {
    return fmt.Errorf("in fileChecker: %w", err)
  }
  f.Close()
  return nil
}
```

**Is and As**
These are 2 functions from the error package. The **Is** function does the expected.

The errors.As function allows you to check if a returned error (or any error it wraps) matches a specific type. It takes in two parameters. The first is the error being examined and the second is a pointer to a variable of the type that you are looking for. If the function returns true, an error in the error chain was found that matched, and that matching error is assigned to the second parameter. If the function returns false, no match was found in the error chain.

If the second parameter to errors.As is anything other than a pointer to an error or a pointer to an interface, the method panics.

Let’s try it out with MyErr:
```go
err := AFunctionThatReturnsAnError()
var myErr MyErr
if errors.As(err, &myErr) {
  fmt.Println(myErr.Code)
}
```

Note that you use var to declare a variable of a specific type set to the zero value. You then pass a pointer to this variable into errors.As.

# Package

**Internal package**
When you create a package called internal, the exported identifiers in that package and its subpackages are only accessible to the direct parent package of internal and the sibling packages of internal.

**Init function**
Every Go package can optionally have a private function named init() that is automatically executed at the beginning of the execution time.

The init() function is a private function by design, which means that it cannot be called from outside the package in which it is contained. Additionally, as the user of a package has no control over the init() function, you should think carefully before using an init() function in public packages or changing any global state in init().

When you declare a function named init that takes no parameters and returns no values, it runs the first time the package is referenced by another package. Since init functions do not have any inputs or outputs, they can only work by side effect, interacting with package-level functions and variables.

init() can be very useful when you want to set up some unexported internal variables. As an example, you might find the current time zone in init(). Database driver packages use init functions to register the database driver.

However, you don’t use any of the identifiers in the package. As mentioned earlier, Go doesn’t allow you to have unused imports. To work around this, Go allows blank imports, where the name assigned to an import is the underscore (_).
```go
import (
  "database/sql"
  _ "github.com/lib/pq"
)
```

However, this pattern is considered obsolete because it’s unclear that a registration operation is being performed. The primary use of init functions today is to initialize package-level variables that can’t be configured in a single assignment.

## Modules

NB Go modules were first introduced in Go version 1.11.

A module is like a packackage with a version. Go uses semantic versioning for versioning modules.

A module by definition is a collection of related packages with **go.mod** at its root.  The **go.mod** file defines the:

* Module import path.
* The version of go with which the module is created.
* Dependency requirements of the module for a successful build. It defines both project’s dependencies requirement and also locks them to their correct version.

NB Both go.mod as well as go.sum files should be checked into the Version Control System (VCS) such as git.

**go.mod**
go.mod file contains the dependency requirements of the module. A dependency of a module can be of two kind:

* Direct -A direct dependency is a dependency which the module directly imports.
* Indirect – It is the dependency that is imported by the module’s direct dependencies. Also, any dependency that is mentioned in the go.mod file but not imported in any of the source files of the module is also treated as an indirect dependency.

go.mod file only records the direct dependency. However, it may record an indirect dependency in the below case:

* Any indirect dependency which is not listed in the go.mod file of your direct dependency or if direct dependency doesn’t have a go.mod file, then that dependency will be added to the go.mod file with indirect as the suffix.

**go.sum**
This file lists down the checksums of direct and indirect dependencies required along with their version. It is to be mentioned that the go.mod file is enough for a successful build.

The checksum present in go.sum file is used to validate the checksum of each direct and indirect dependency to confirm that none of them have been modified.

**Imports**
Go supports namespace aliases on imports:
```go
import (
  crand "crypto/rand"
  "encoding/binary"
  "fmt"
  "math/rand"
)
```

The package name . places all the exported identifiers in the imported package into the current package’s namespace; you don’t need a prefix to refer to them (**discouraged**! Do use prefixes).

You can also use _ as the package name.

As usual, import convention seems to be handled by the gofmt tool. Still, try to be consistent.

**Exports from Modules**
A.k.a. visibility outside the module is not declared via a special (e.g. export) keyword. Instead, Go uses capitalization to determine if a package-level identifier is visible outside of the package where it is declared.

An identifier whose name starts with an uppercase letter is exported. Conversely, an identifier whose name starts with a lowercase letter or underscore can only be accessed from within the package where it is declared.

**Create a Module**
This creates a new (empty) module:
```bash
go mod init
go mod init myModule
```

## Module CLI Usage

**Update modules/dependencies**
This cmd will download all the dependencies that are required in your source files and update go.mod file with that dependency:
```bash
go mod tidy
```

**Create a module**
This will create the appropriate go.mod module definition file in the current dir:
```bash
go mod init mymodule
```

# Linters

Golang has its official source code auto/re-formating tool called gofmt.

## Visual Studio Code linter tools

https://github.com/golang/vscode-go/blob/HEAD/docs/tools.md

VSCode Go plugin uses the staticcheck (by default), golangci-lint, and revive (which is golint enhanced) linters.

## Linters at Uber

https://github.com/uber-go/guide/blob/master/style.md#linting

Uber recommends using the following linter tools:

* errcheck to ensure that errors are handled
* goimports to format code and manage imports
* golint to point out common style mistakes
* govet to analyze code for common mistakes
* staticcheck to do various static analysis checks

## Lint Runners

**golangci-lint**
This is the go-to lint runner for Go code, largely due to its performance in larger codebases and ability to configure and use many canonical linters at once.

**Variable shadowing detection**
Use this with go vet to detect shadowed variables in your code:
```bash
go install golang.org/x/tools/go/analysis/passes/shadow/cmd/shadow@latest
```

# Testing

The testing package in the standard library provides the types and functions to write tests, while the go test tool that’s bundled with Go runs your tests and generates reports.

Go tests are placed in the same directory and the same package as the code. Every test is written in a file whose name ends with _test.go. If you are writing tests against foo.go, place your tests in a file named foo_test.go.

Test functions start with the word Test and take in a single parameter of type *testing.T. By convention, this parameter is named t. Test functions do not return any values.

The name of the test (apart from starting with the word 'Test') is meant to document what you are testing, so pick something that explains what you are testing. When writing unit tests for individual functions, the convention is to name the unit test Test followed by the name of the function. When testing unexported functions, some people use an underscore between the word Test and the name of the function.

**Running Tests**
The go test command allows you to specify which packages to test. Using ./... for the package name specifies that you want to run tests in the current directory and all of the subdirectories of the current directory.
NB use -v flag for verbose output.
```bash
go test
go test ./ -v
```

**Setting Up and Tearing Down**
Use a TestMain function to manage this state and run your tests:
```go
var testTime time.Time

func TestMain(m *testing.M) {
  fmt.Println("Set up stuff for tests here")
  testTime = time.Now()
  exitVal := m.Run()
  fmt.Println("Clean up stuff after tests here")
  os.Exit(exitVal)
}

func TestFirst(t *testing.T) {
  fmt.Println("TestFirst uses stuff set up in TestMain", testTime)
}
```

Running go test on a package with a TestMain function calls the function instead of invoking the tests directly. Once the state is configured, call the Run method on *testing.M to run the test functions. The Run method returns the exit code; 0 indicates that all tests passed. Finally, you must call os.Exit with the exit code returned from Run.

**Code Coverage**
Adding the -cover flag to the go test command calculates coverage information and includes a summary in the test output. If you include a second flag -coverprofile, you can save the coverage information to a file:
```bash
go test -v -cover -coverprofile=c.out
```

The cover tool included with Go generates an HTML representation of your source code with that information:
```bash
go tool cover -html=c.out
```

**Race Checker**
Go includes a race checker (even if it isn’t guaranteed to find every single data race in your code). Use the flag -race with go test to enable it:
```bash
go test -race
```

## Stubs

**httptest**
The Go standard library includes the net/http/httptest package to make it easier to stub HTTP services.

# Other issues

**Variable names**
Variable, function etc names should convey some info about them. The official docs in Golang say that short and meaningless variable names like 'c', 'a', 'i' are encouraged (especially in loops). However, you can always use a little more expressive ones, like 'count', 'sumTotal' etc. The goal is to aid your understanding of your own code when revisiting it 6 months later.

**Missing godoc**
Do make an effort to provide package, or at least top-level function godoc comments.

**Not using std Go lib functions**
Do not write your own implementation of commonly used functionality if you don't have to, e.g. for string trimming use strings.Trim(test, " ") or the related TrimLeft, TrimRight functions. Instead of using + for PATH/filename concatenation, use filepath.Join from the standard "path/filepath" mod.

This avoids bugs, reduces code length/duplication etc.

**Import convention**
The gofmt tool and linters supposedly take care most of the coding style related issues.

Still, try to be consistent in things like:
- order of the imported packages (e.g. std packages first, then local packages, and 3rd party packages last)
- number of newlines between code sections (import block, top-level definitions)

**Structs and method functions belong together**
In object-oriented languages the data and the related operations are held closely together (in a class). In Golang (similar to Rust, Haskell, Clojure), the struct definition (representing the data) and the method functions defined on them (defining the operations on data) should be kept in proximity to each other, too, to aid readibility etc.

**Pointers**
TODO How exactly does Golang deal with it's pointers (besides having a GC).

**Unit Tests**
TODO things like setup/teardown blocks, coverage metrics, how much TDD to do.
