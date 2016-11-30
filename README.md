![gopher](http://golang-jp.org/doc/gopher/talks.png)

# echoフレームワークをAWS EC2環境で動かすサンプル


## echoとは

![echo](https://echo.labstack.com/images/logo.png)

echoは Goで作られている軽量なフレームワークです。

- https://echo.labstack.com/guide
- https://github.com/labstack/echo


## 実行環境

```
Amazon Linux AMI
t2.micro
1 コア vCPU (最大 3.3 GHz)、1 GiB メモリ RAM、8 GB ストレージ
```

## Go1.6のインストール

```bash
$ sudo -i
# mkdir -p /usr/local/go
# cd /usr/local
# wget https://storage.googleapis.com/golang/go1.6.linux-amd64.tar.gz
# tar -C /usr/local -xzf go1.6.linux-amd64.tar.gz
# chmod -R 777 /usr/local/go
# exit
```

```bash
$ vi $HOME/.bash_profile
--------------------------------------------------
     :     追記
export GOVERSION=1.6
export GOROOT=/usr/local/go
export GOPATH=$HOME/go/$GOVERSION
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
--------------------------------------------------
```

```bash
$ source $HOME/.bash_profile
```

```bash
$ go version
go version go1.6 linux/amd64
```

```bash
$ mkdir -p $GOPATH/src
```

## Glideのインストール

```bash
$ curl https://glide.sh/get | sh
$ glide -v
glide version v0.12.3
```

## サンプルプロジェクト作成

Githubからソースコードをクローンします

```bash
$ cd $GOPATH/src
$ git clone https://github.com/hayao56/goecho.git
$ cd goecho
```

## 依存パッケージのインストール

```bash
$ glide install
```

```bash
$ tree vendor/ -L 3
vendor/
|-- github.com
|   |-- labstack
|   |   |-- echo
|   |   `-- gommon
|   |-- mattn
|   |   |-- go-colorable
|   |   `-- go-isatty
|   |-- miekg
|   |   `-- dns
|   |-- rsc
|   |   `-- letsencrypt
|   |-- tylerb
|   |   `-- graceful
|   |-- valyala
|   |   `-- fasttemplate
|   `-- xenolf
|       `-- lego
|-- golang.org
|   `-- x
|       |-- crypto
|       |-- net
|       |-- sys
|       `-- time
`-- gopkg.in
    `-- square
        `-- go-jose.v1
```


## echoサーバー起動

```bash
$ go run server.go
  http server started on :1234
```

## サーバーアクセス

```bash
$ curl http://localhost:1234
Hello, World!

```

