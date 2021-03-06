### File writer

#### Creates a file with the specified name with access for writing and with shared access for renaming.

---------------------------------------

### Overview

```go
func New(name string, perm os.FileMode) *file
```
New returns a pointer to the new file.

```go
func Write(p []byte) (n int, err error)
```
Implementation of the Writer interface.

```go
func Create() error
```
Create closes, if necessary, and creates a file with the above name.

```go
func Close()
```
Close file.


---------------------------------------

#### Example of a log rotation:

```go
package main

import (
	"fmt"
	"github.com/y4v8/filewriter"
	"log"
	"os"
	"time"
)

func main() {
	const name = "std.log"

	file := filewriter.New(name, 0666)
	if err := file.Create(); err != nil {
		panic(err)
	}
	defer file.Close()

	log.SetOutput(file)

	i, n := 0, 0
	rotate := time.Tick(time.Millisecond * 100)
	tick := time.Tick(time.Millisecond * 5)
	end := time.After(time.Second * 5)
	for {
		select {
		case <-rotate:
			// external rename
			fileName := fmt.Sprintf("%03d.%s", n, name)
			os.Rename(name, fileName)

			file.Create()
			n++
		case <-tick:
		case <-end:
			return
		}
		log.Printf("%03d", i)
		i++
	}
}
```
