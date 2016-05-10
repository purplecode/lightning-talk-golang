# Intro

Go (often referred to as golang) is an open source programming language created at Google[13] in 2007 by Robert Griesemer, Rob Pike, and Ken Thompson.

It is a compiled, statically typed language in the tradition of Algol and C, with garbage collection, limited structural typing, memory safety features and CSP-style (Communicating sequential processes) concurrent programming features added.


# Hello World

                package main

 

                import "fmt"

 

                func main() {

                                fmt.Println("Hello, World")

                }


# tooling

gofmt - formatter

golint - linter

godoc - documentation

go test/build/clean/run/...

go get

go vet - static analyzer


# Language Pros

Optional concise variable declaration and initialization through type inference (x := 0 not int x = 0;).

Fast compilation times.[26]

Remote package management (go get)[27] and online package documentation

Built-in concurrency primitives: light-weight processes (goroutines), channels, and the select statement.

Functions may return multiple values, and returning a result, err pair is the conventional way

 

# Language Cons

lack of compile-time generics leads to code duplication, metaprogramming cannot be statically checked[30][self-published source?][31][self-published source?] and standard library cannot offer generic algorithms[32]

lack of language extensibility (like operator overloading) makes certain tasks more verbose[33][self-published source?][30][self-published source?]

the type system's lack of const or Hindley-Milner typing inhibits safety and/or expressiveness[34][self-published source?][35][self-published source?]

the pauses and overhead of garbage collection limit Go's use in systems programming compared to languages with manual memory management

no inheritance, generic programming, assertions, pointer arithmetic, and implicit type conversions (by compiler).

 

# Usage

                Docker

                Ethereum

                @https://en.wikipedia.org/wiki/Go_(programming_language)

                Google, for many projects, notably including download server dl.google.com[83][84][85]

                Dropbox, migrated some of their critical components from Python to Go[86]

                CloudFlare, for their delta-coding proxy Railgun, their distributed DNS service, as well as tools for cryptography, logging, stream processing, and accessing SPDY sites.[87][88]

                SoundCloud, for "dozens of systems"[89]

                Cloud Foundry, a platform as a service

                CoreOS, a Linux-based operating system that utilizes Docker containers.[91]

                Couchbase, Query and Indexing services within the Couchbase Server[92]

                MongoDB, tools for administering MongoDB instances[94]

                Uber, for handling high volumes of geofence-based queries.[100]

 

# Number of packages

               

# Naming dispute

                @https://en.wikipedia.org/wiki/Go_(programming_language)#Naming_dispute

                @https://en.wikipedia.org/wiki/Go!_(programming_language)

 

# Logo Go gopher

                @https://blog.golang.org/gopher

 

# types

                byte, int64, float32, etc.

                boolean

                string (immutable)

                custom types (struct)

                                type Circle struct {

                                                radius float64

                                }

                                type ipv4addr uint32

                arrays

                hash tables

                channels

                pointers

                nil === null

 

# functions

                func log(handler http.Handler) http.Handler {

                                return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {

                                                log.Printf("%s %s %s", r.RemoteAddr, r.Method, r.URL)

                                                handler.ServeHTTP(w, r)

                                })

                }

 

# interfaces

 

                interface{} ~~ Object

 

                type geometry interface {

                                area() float64

                                perim() float64

                }

                type rect struct {

                                width, height float64

                }

                type circle struct {

                                radius float64

                }

 

                func (r rect) area() float64 {

                                return r.width * r.height

                }

                func (r rect) perim() float64 {

                                return 2*r.width + 2*r.height

                }

 

                func (c circle) area() float64 {

                                return math.Pi * c.radius * c.radius

                }

                func (c circle) perim() float64 {

                                return 2 * math.Pi * c.radius

                }

 

                func measure(g geometry) {

                                fmt.Println(g)

                                fmt.Println(g.area())

                                fmt.Println(g.perim())

                }

 

                func main() {

                                r := rect{width: 3, height: 4}

                                c := circle{radius: 5}

                                measure(r)

                                measure(c)

                }

 

# package manager? omg...

                https://github.com/golang/go/wiki/PackageManagementTools

                http://jbeckwith.com/2015/05/29/dependency-management-go/

                1. REPEATABLE BUILDS

                2. ISOLATED ENVIRONMENTS

                3. CONSENSUS

                Having a package management story is awesome. What’s even better is making sure everyone uses the same one :) As long as developers are inventive and curious, there will always be alternatives. But there needs to be consensus on the community accepted standard on how a package manager will work. If 5 projects use 5 different models of dependency management, we’re all out of luck.

 

                In Go's package system, each package has a path (e.g., "compress/bzip2" or "golang.org/x/net/html") and a name (e.g., bzip2 or html). References to other packages' definitions must always be prefixed with the other package's name, and only the capitalized names from other packages are accessible: io.Reader is public but bzip2.reader is not.[59] The go get command can retrieve packages stored in a remote repository such as GitHub, and package paths often look like partial URLs for compatibility

 

               

# composition

                type Person struct {

                                name string

                                age int

                }

 

                func (p Person ) hello() {

                                fmt.Printf("Hi, I am %s\n", p.name)

                }

 

                func (p Person) details() {

                                fmt.Printf("[name: %s, age: %d]\n", p.name,p.age)

                }

 

                type PersonWithPhone struct {

                                Person

                                number int

                }

 

                type PersonWithSmartPhone struct {

                                PersonWithPhone

                                ip int32

                }

 

                func mains() {

                                p := Person{"a", 12, "", ""}

                                fmt.Print(p)

 

                                s := PersonWithPhone{}

                                s.age += 9

                                fmt.Print(s)

 

                                sb := PersonWithSmartPhone{}

                                sb.name

                                fmt.Print(sb)

                }

               

# Concurrency: goroutines and channels

                primary concurrency construct is the goroutine, a type of light-weight process

                The language specification does not specify how goroutines should be implemented, but current implementations multiplex a Go process's goroutines onto a smaller set of operating system threads

                channels, optional buffers

               

                Goroutines  are like threads

                Share memory

                But cheaper:

                Smaller, segmented stacks.

                Many goroutines per OS thread.

 

               

                @https://en.wikipedia.org/wiki/Go_(programming_language)

                package main

 

                import (

                                "fmt"

                                "time"

                )

 

                func readword(ch chan string) {

                                fmt.Println("Type a word, then hit Enter.")

                                var word string

                                fmt.Scanf("%s", &word)

                                ch <- word

                }

 

                func timeout(t chan bool) {

                                time.Sleep(5 * time.Second)

                                t <- true

                }

 

                func main() {

                                t := make(chan bool)

                                go timeout(t)

 

                                ch := make(chan string)

                                go readword(ch)

 

                                select {

                                case word := <-ch:

                                                fmt.Println("Received", word)

                                case <-t:

                                                fmt.Println("Timeout.")

                                }

                }

               

 

# exceptions

                After initially omitting exceptions, the exception-like panic/recover mechanism was eventually added to the language

                                f, err := os.Open("filename.ext")

                                if err != nil {

                                                log.Fatal(err)

                                }

# defer

                @https://blog.golang.org/defer-panic-and-recover

                func CopyFile(dstName, srcName string) (written int64, err error) {

                                src, err := os.Open(srcName)

                                if err != nil {

                                                return

                                }

                                defer src.Close()

 

                                dst, err := os.Create(dstName)

                                if err != nil {

                                                return

                                }

                                defer dst.Close()

 

                                return io.Copy(dst, src)

                }

               

                // 0

                                func a() {

                                i := 0

                                defer fmt.Println(i)

                                i++

                                return

                }

               

                // "3210":

                func b() {

                                for i := 0; i < 4; i++ {

                                                defer fmt.Print(i)

                                }

                }

               

                // 2

                func c() (i int) {

                                defer func() { i++ }()

                                return 1

                }

               

# panic, defer

                @https://blog.golang.org/defer-panic-and-recover

                func main() {

                                f()

                                fmt.Println("Returned normally from f.")

                }

 

                func f() {

                                defer func() {

                                                if r := recover(); r != nil {

                                                                fmt.Println("Recovered in f", r)

                                                }

                                }()

                                fmt.Println("Calling g.")

                                g(0)

                                fmt.Println("Returned normally from g.")

                }

 

                func g(i int) {

                                if i > 3 {

                                                fmt.Println("Panicking!")

                                                panic(fmt.Sprintf("%v", i))

                                }

                                defer fmt.Println("Defer in g", i)

                                fmt.Println("Printing in g", i)

                                g(i + 1)

                }

                               

               

# project structure

                export GOPATH=$HOME/work

                export PATH=$PATH:$GOPATH/bin

               

                bin/

                                hello                           # command executable

                pkg/

                                linux_amd64/

                                                github.com/golang/example/

                                                                stringutil.a            # package object

                                                github.com/user/

                                                                stringutil.a            # package object

                src/

                                github.com/golang/example/

                                .git/                       # Git repository metadata

                                                hello/

                                                                hello.go                # command source

                                                stringutil/

                                                                reverse.go              # package source

                                                                reverse_test.go         # test source

                                github.com/user/

                                                hello/

                                                                hello.go                # command source

                                                stringutil/

                                                                reverse.go              # package source

                                                                reverse_test.go         # test source

 

 

# testing

                import "testing"

               

                func TestTimeConsuming(t *testing.T) {

                                if testing.Short() {

                                                t.Skip("skipping test in short mode.")

                                }

                                //

                                t.Fail()

                                //

                                t.Error("I'm in a bad mood.")

                }

 

 

# Live demo

                @https://github.com/docker/docker

 