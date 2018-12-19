# sshtunnel

[![](https://godoc.org/github.com/sgreben/sshtunnel?status.svg)](http://godoc.org/github.com/sgreben/sshtunnel) [![](https://goreportcard.com/badge/github.com/sgreben/sshtunnel/goreportcard)](https://goreportcard.com/report/github.com/sgreben/sshtunnel) [![cover.run](https://cover.run/go/github.com/sgreben/sshtunnel.svg?style=flat&tag=golang-1.10)](https://cover.run/go?tag=golang-1.10&repo=github.com%2Fsgreben%2Fsshtunnel) [![Build Status](https://travis-ci.org/sgreben/sshtunnel.svg?branch=master)](https://travis-ci.org/sgreben/sshtunnel)

Go library providing a dialer for SSH-tunneled TCP and Unix domain socket connections. Please note the [**limitations**](#limitations) below.

```go
import "github.com/sgreben/sshtunnel"
```

- [Get it](#get-it)
- [Use it](#use-it)
	- [Docs](#docs)
	- [Toy example](#toy-example)
	- [Bigger example](#bigger-example)
- [Limitations](#limitations)

## Get it

```sh
go get -u "github.com/sgreben/sshtunnel"
```

## Use it

### Docs

[![](https://godoc.org/github.com/sgreben/sshtunnel?status.svg)](http://godoc.org/github.com/sgreben/sshtunnel)

### Toy example

```go
package main

import (
	"fmt"
	"io"
	"os"

	"github.com/sgreben/sshtunnel"
)

func main() {
	// Connect to "google.com:80" via a tunnel to "ubuntu@my-ssh-server-host:22"
	keyPath := "private-key.pem"
	tunnelConfig := sshtunnel.Config{
		SSHAddr: "my-ssh-server-host:22",
		Auth: sshtunnel.ConfigAuth{
			UserName: "ubuntu",
			Keys:     []sshtunnel.ConfigAuthKey{{Path: &keyPath}},
		},
	}
	conn, _, err := sshtunnel.Dial("tcp", "google.com:80", &tunnelConfig)
	if err != nil {
		panic(err)
	}
	// Do things with conn
	fmt.Fprintln(conn, "GET /")
	io.Copy(os.Stdout, conn)
}
```

### Bigger example

See [docker-compose-hosts](https://github.com/sgreben/docker-compose-hosts).

## Limitations

- **Host key verification is disabled** when using the "simple config", and can only be configured when using a raw `ssh.ClientConfig`.
- **No tests**; want some - write some.