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

## タイムゾーン変更

AWSは、すべてのリージョンの初期設定が **UTC**

```bash
ls -l /usr/share/zoneinfo/|grep Japan
-rw-r--r--  2 root root   333 11月 11 00:31 Japan
```
```bash
sudo vim /etc/sysconfig/clock
--------------------------------------------------
#ZONE="UTC"
ZONE="Japan"
UTC=true
--------------------------------------------------
```
```bash
sudo ln -sf /usr/share/zoneinfo/Japan /etc/localtime
sudo reboot
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

$ go get golang.org/x/tools/cmd/goimports
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
(2回目以降は glide update)
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



## Elastic IPの割り当て

```
1. EC2ダッシュボードの左側メニューから、Elastic IPを選択
2. [新しいアドレスの割り当て]をクリック
3. 確認画面が出ますので、[関連付ける]を選択
4. Elastic IPが取得されましたとメッセージが出るので、閉じるをクリック
5. 取得したElastic IPを選択した状態で、[アクション]から、アドレスの関連付けを選択
6. アドレスの関連付けの画面が開くので、[インスタンス]をクリックすると、現在稼働しているインスタンスが表示され、
関連付けるインスタンスを選択すると、プライベートIPアドレスも自動で変更されます。
7. [関連付ける]をクリック
```

## セキュリティーグループの設定

```
1. インスタンスのセキュリティグループをクリック
2. インバウンドのタブを選択
3. 編集をクリック
4. ルールの追加をクリック
  - タイプ:カスタム TCP ルール
  - プロトコル:TCP
  - ポート範囲:1234
  - 送信先:0.0.0.0/0
```

## Route53にネームサーバー追加

```
1. Route 53をクリック
2. 「Create Hosted Zone」をクリック
3. 「Domain Name」にドメインを入力、「Public Hosted Zone」を選択してCreateをクリック
4. NS、SOAのTTL(Seconds)を「300」に変更
（こちらが反映されるまでの時間となる）
```

## ネームサーバーにAレコード追加

```
1. 対象のドメインを選択して「Go to Record Sets」をクリック
2. Type」の「A  IPv4 address」を選択して、パブリックIPを入力、「Create」をクリック

```

## レジストラのネームサーバーを変更

お名前.comなどを設定

```
1. NSのネームサーバーをすべて設定
  
例）
  - ns-XXXX.awsdns-XX.org
  - ns-XXXX.awsdns-XX.co.uk
  - ns-XXX.awsdns-XX.net
  - ns-XXX.awsdns-XX.com

```
5～10分ほどで、nslookupでドメインからIPが引けるようになります。
以上、AWSのechoサーバーにドメインでアクセスができます。

