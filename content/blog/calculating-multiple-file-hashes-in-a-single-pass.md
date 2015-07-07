+++
date = "2015-07-07T11:14:06-04:00"
title = "Calculating Multiple File Hashes in a Single Pass"
type = "post"
tags = [ "golang", "go" ]
+++

We do a lot of file hash calculations at work, where we commonly go through millions of files a day using a diverse number of hashing algorithms. The standard Go library is amazing, and it has many structures and methods to do all that kind of stuff. Sometimes you just have to look for some new methods that opens up the possibilities even more.

### The Goal

The initial goal of this code was to calculate multiple hashes on a single file residing on disk, and only perform a single read, instead of reading the whole contents of the file multiple times for each hash algorithm.

The idea was to return an structure with the results of the desired hash:

```go
type HashInfo struct {
	Md5    string `json:"md5"`
	Sha1   string `json:"sha1"`
	Sha256 string `json:"sha256"`
	Sha512 string `json:"sha512"`
}
```

### Looking into the standard library

As park of the Go standard library ```io``` package, we can find this function below:

```go
func MultiWriter(writers ...Writer) Writer
```

Here is a snippet of the source code implementation of this function in the ```io``` package:

```go
type multiWriter struct {
    writers []Writer
}

func (t *multiWriter) Write(p []byte) (n int, err error) {
	for _, w := range t.writers {
		n, err = w.Write(p)
		if err != nil {
			return
		}
		if n != len(p) {
			err = ErrShortWrite
			return
		}
	}
	return len(p), nil
}

func MultiWriter(writers ...Writer) Writer {
	w := make([]Writer, len(writers))
	copy(w, writers)
	return &multiWriter{w}
}
```

The MultiWriter method creates a writer that duplicates its writes to all the provided writers, similar to the Unix ```tee``` command.

This is interesting because since all of hash functions in standard library adheres to this interface:

```go
type Hash interface {
        // Write (via the embedded io.Writer interface) adds more data to the running hash.
        // It never returns an error.
        io.Writer

        // Sum appends the current hash to b and returns the resulting slice.
        // It does not change the underlying hash state.
        Sum(b []byte) []byte

        // Reset resets the Hash to its initial state.
        Reset()

        // Size returns the number of bytes Sum will return.
        Size() int

        // BlockSize returns the hash's underlying block size.
        // The Write method must be able to accept any amount
        // of data, but it may operate more efficiently if all writes
        // are a multiple of the block size.
        BlockSize() int
}
```

### The Approach

Therefore, we could create a ```MultiWriter``` that is going to write to multiple Hash implementations only performing a single read pass through the original file, as you can see in the code below:

```go
func CalculateBasicHashes(rd io.Reader) HashInfo {

	md5 := md5.New()
	sha1 := sha1.New()
	sha256 := sha256.New()
	sha512 := sha512.New()

	// For optimum speed, Getpagesize returns the underlying system's memory page size.
	pagesize := os.Getpagesize()

	// wraps the Reader object into a new buffered reader to read the files in chunks
	// and buffering them for performance.
	reader := bufio.NewReaderSize(rd, pagesize)

	// creates a multiplexer Writer object that will duplicate all write
	// operations when copying data from source into all different hashing algorithms
	// at the same time
	multiWriter := io.MultiWriter(md5, sha1, sha256, sha512)

	// Using a buffered reader, this will write to the writer multiplexer
	// so we only traverse through the file once, and can calculate all hashes
	// in a single byte buffered scan pass.
	//
	_, err := io.Copy(multiWriter, reader)
	if err != nil {
		panic(err.Error())
	}

	var info HashInfo

	info.Md5 = hex.EncodeToString(md5.Sum(nil))
	info.Sha1 = hex.EncodeToString(sha1.Sum(nil))
	info.Sha256 = hex.EncodeToString(sha256.Sum(nil))
	info.Sha512 = hex.EncodeToString(sha512.Sum(nil))

	return info
}
```

Here is a sample of command line utility to calculate the multiple hashes.

```go
package main

import (
	"bufio"
	"crypto/md5"
	"crypto/sha1"
	"crypto/sha256"
	"crypto/sha512"
	"encoding/hex"
	"fmt"
	"io"
	"log"
	"os"
	"runtime"
)

func main() {
	args := os.Args[1:]

	var filename string
	filename = args[0]

    // open an io.Reader from the file we would like to calculate hashes
	f, err := os.OpenFile(filename, os.O_RDONLY, 0)
	if err != nil {
		log.Fatalln("Cannot open file: %s", filename)
	}
	defer f.Close()

	info := CalculateBasicHashes(f)

	fmt.Println("md5    :", info.Md5)
	fmt.Println("sha1   :", info.Sha1)
	fmt.Println("sha256 :", info.Sha256)
	fmt.Println("sha512 :", info.Sha512)
	fmt.Println()
}
```

Of course that in a real-world scenario we wouldn't be invoking the command line utility for every single file. This was just a simple example on how to write a little command line utility to demonstrate this approach. The real benefit is when we are traversing through millions of files and performing hash calculations using a single read pass through the contents file. This has a significant impact on our ability to fast go through our file repositories.

There are so many interesting functions and interfaces in the standard library that everyone should take look at the source code once in a while.
