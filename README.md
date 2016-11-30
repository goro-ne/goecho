![gopher](http://golang-jp.org/doc/gopher/talks.png)


# Go1.6のインストール

```bash
$ sudo -i
# mkdir -p /usr/local/go
# cd /usr/local
# wget https://storage.googleapis.com/golang/go1.6.linux-amd64.tar.gz
# tar -C /usr/local -xzf go1.6.linux-amd64.tar.gz
# chmod -R 777 /usr/local/go
# exit
$ vi $HOME/.bash_profile
--------------------------------------------------
     :     追記
export GOVERSION=1.6
export GOROOT=/usr/local/go
export GOPATH=/home/dev/go/$GOVERSION
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
source $HOME/.bash_profile
--------------------------------------------------
$ go version
go version go1.6 linux/amd64

$ mkdir -p $HOME/go/$GOVERSION
```

# Glideのインストール

```bash
$ curl https://glide.sh/get | sh
$ glide -v
glide version v0.12.3
```

# プロジェクト作成

Githubからソースコードをクローンします

```bash
$ cd $GOPATH/src
$ cd /home/dev/go/1.6/src/github.com
$ git clone https://github.com/hayao56/goecho.git
$ cd goecho
```

# echoサーバーサンプル

```bash
$ vi server.go
--------------------------------------------------
package main

import (
	"net/http"
	"github.com/labstack/echo"
)

func main() {
	e := echo.New()
	e.GET("/", func(c echo.Context) error {
		return c.String(http.StatusOK, "Hello, World!")
	})
	e.Logger.Fatal(e.Start(":1323"))
}
--------------------------------------------------
```

# glideパッケージ作成

```bash
glide create
```

```bash
vi glide.yaml
--------------------------------------------------
package: goecho
import:
- package: github.com/labstack/echo
  version: ~3.0.2
--------------------------------------------------
glide install
```

# echoサーバー起動

```bash
$ go run server.go
  http server started on :1323
```

```bash
$ :>vendor/.gitkeep
$ vi .gitignore
--------------------------------------------------
.DS_Store
*~
*.swp
/vendor/*
!/vendor/.gitkeep
--------------------------------------------------

$ git add ANYANY
$ git commit -m "add echo server"
$ git branch --unset-upstream

$ git push -u origin master
```

# サーバーアクセス

```bash
$ curl http://localhost:1323
Hello, World!

```

