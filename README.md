# Setup_FlaskServer_on_Ubuntu
<br>
Ubuntuで，https通信を行うFlaskサーバ構築をしてたら沼にはまった<br>
<br>

> [!CAUTION]
> 実行環境は，仮想マシン(VMware) : Ubuntu22.04


## Flaskのインストール

```
$ sudo -s
# apt update
# apt upgrade
```

```
# apt install python3-pip 
# pip install flask
```

## サーバファイルの構築

> [!WARNING]
> 作業ディレクトリは，実験環境に合わせて構築すること


```
# mkdir Files
# cd Files/
# vim server.py
```

> [!TIP]
> server.pyには以下を記述


```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
	return "Hello World!!"

if __name__=="__main__":
	app.run(port=443, debug=True, ssl_context=('/etc/ssl/private/server.crt','/etc/ssl/private/server.key'),host='0.0.0.0')


```

## 証明書の作成

> [!WARNING]
> 証明書の項目とIPアドレスは，実験環境に合わせて設定すること

```
# cd /etc/ssl/private/
# openssl genrsa -out server.key
# openssl req -new -key server.key -out server.csr -subj "/C=JP/ST=**/L=**/O=**/CN=mytest.example.com"
# cat << 'EOS' > sat.txt
subjectAltName = DNS:mytest.example.com, IP:**.**.**.**
EOS
# openssl x509 -req -days 3650 -in server.csr -signkey server.key -out server.crt -extfile san.txt
# openssl x509 -text -in server.crt -noout
```

## サーバの起動

```
# python3 server.py
* Serving Flask app 'server'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on https://127.0.0.1:443
 * Running on https://***.***.***.***:443
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: ***-***-***
```

## https接続確認

> [!WARNING]
> 別コンソールを起動する

```
# curl -v --cacert server.crt https://***.***.***.***
...
...
...
> GET / HTTP/1.1
> Host: ***.***.***.***
> User-Agent: curl/7.81.0
> Accept: */*
...
...
...
Hello World!!
```

<b>Acceptかつ，Hello Worldと表示されればOK!!</b><br>

## 参考

- UbuntuでFlaskを構築<br>
https://zenn.dev/yonishi/scraps/c48aa2fa163174<br>

- Flaskでhttpsサーバを立てる方法<br>
https://tex2e.github.io/blog/python/flask-https<br>

- サーバ名がIPアドレスの場合の証明書作成/サブジェクト代替名(SAN)の設定方法<br>
https://tex2e.github.io/blog/protocol/certificate-with-ip-addr<br>


