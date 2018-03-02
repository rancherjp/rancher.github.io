* * *

title: Rancher サーバーのインストール layout: rancher-default-v1.3 version: v1.3 lang: ja redirect_from: - /rancher/installing-rancher/installing-server/ - /rancher/latest/en/installing-rancher/installing-server/

* * *

## ## Rancherサーバーのインストール

Rancher は、Docker コンテナーのセットで配布されています。Rancher を簡単に動かすのは、2つのコンテナーを動かすだけです。1つ目のコンテナーは管理サーバーとして、もう一つはノードのエージェントとなります。

* [Rancher サーバー - シングルコンテナ (HA無し)](#single-container)
* [Rancher サーバー - シングルコンテナ (HA無し) - 外部データベース](#single-container-external-database)
* [Rancher サーバー - シングルコンテナー (HA無し)- MySQLボリュームをバインドによりマウント](#single-container-bind-mount)
* [Rancher サーバー - 完全アクティブ/アクティブ HA](#multi-nodes)

> **ノート:** `docker run rancher/server --help`で、Rancherサーバーコンテナーの構成オプションのヘルプを全て見ることができます。

### 必要要件

* Docker 1.10.3以上をサポートするLinuxディストリビューション [RancherOS](http://docs.rancher.com/os/), Ubuntu, RHEL/CentOS 7 は重点的にテストされています。 
  * RHEL/CentOS の デフォルトストレージドライバー(つまり、loopback を使った devicemapper)は、[Docker](https://docs.docker.com/engine/reference/commandline/dockerd/#/storage-driver-options)社から推奨されていません。 これを変更する方法については、Docker ドキュメントを参照してください。
* 1GB RAM
* MySQL サーバー は max_connections > 150 に設定してください。 
  * MySQL 設定の必要要件  
    * オプション1：Antelope フォーマットで動かす場合は、デフォルトの`COMPACT`
    * オプション2：MySQL 5.7 で Barracuda フォーマットを使う場合は、`ROW_FORMAT` を `Dynamic`

> **注：** 現在、Docker for Windows と Docker for Mac は、Rancher をサポートしていません。

### Rancher サーバータグ

Rancher サーバーには2種類のタグがあります。それぞれのメジャーリリースタグ毎に特定のバージョンに対するドキュメントが提供されます。

* `rancher/server:latest` タグは、最新の開発ビルドに対してつけられます。 これらのビルドは、CI自動化フレームワークを通じて検証されています。 これらのリリースは本番環境での開発用ではありません。
* `rancher/server:stable` タグは、最新の安定版リリースです。このタグは、本番環境に推奨するバージョンです。 

末尾に `rc{n}` の付いているリリースは全て使用しないでください。 これらの `rc` ビルドは、 Rancher チームが開発版ビルドをテストするためのものです。

<a id="single-container"></a>

### Rancher サーバーを起動 - シングルコンテナー(HA無し)

Docker がインストールされている Linux 上で、シングルインスタンスで Rancher を動かすコマンドはシンプルです。

```bash
$ sudo docker run -d --restart=unless-stopped -p 8080:8080 rancher/server
```

### Rancher UI

`8080`の公開ポートから UI と API が利用できるようになります。 Docker イメージがダウンロードされた後、Rancherが正常に表示されるようになるまで1〜2分かかります。

次のURLを表示：`http://<サーバー_IP>:8080`。`http://<サーバー_IP>:8080`は、Rancher サーバーを実行しているホストの公開IPアドレスです。

UIが起動して稼働し始めれば、[ホストを追加]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/)を始めたり、インフラストラクチャーカタログからコンテナーオーケストレーションを選択できます。 通常は、コンテナーオーケストレーションを特に指定しなければ、環境は cattle になります。 Rancher にホストが追加されたら、[Rancherカタログ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/catalog/)からテンプレートを起動するか、[サービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-services/)を追加できるようになります。

<a id="single-container-external-database"></a>

### Rancher サーバーを起動 - シングルコンテナー - 外部データベース

Rancher サーバーに付属している内部データベースを使う代わりに、外部データベースを指定して Rancher サーバーを起動する事もできます。 コマンド自体は同じですが、外部データベースに直接接続する為の引数を追加します。

> **注:** データベース名とデータベースのユーザーは作成済みである必要がありますが、スキーマーを作成しておく必要はありません。 Rancher により関連するスキーマーは全て自動的に作成されます。

データベースとユーザーを作成する SQL コマンドのサンプル例です。

```sql
> CREATE DATABASE IF NOT EXISTS cattle COLLATE = 'utf8_general_ci' CHARACTER SET = 'utf8';
> GRANT ALL ON cattle.* TO 'cattle'@'%' IDENTIFIED BY 'cattle';
> GRANT ALL ON cattle.* TO 'cattle'@'localhost' IDENTIFIED BY 'cattle';
```

外部データベースに接続する Rancher を起動するには、コンテナーのコマンドの一部に追加の引数を渡します。

```bash
$ sudo docker run -d --restart=unless-stopped -p 8080:8080 rancher/server \
    --db-host myhost.example.com --db-port 3306 --db-user username --db-pass password --db-name cattle
```

ほとんどのオプションは、規定値があるので必須ではありません。MySQLサーバー先だけは必須です。

```bash
--db-host               IP or hostname of MySQL server
--db-port               port of MySQL server (default: 3306)
--db-user               username for MySQL login (default: cattle)
--db-pass               password for MySQL login (default: cattle)
--db-name               MySQL database name to use (default: cattle)
```

<br />

> **注:** 以前のバージョンのRancherサーバーでは、外部データベースに接続するために環境変数を利用していました。環境変数はまだ使うことができますが、Rancher ではコマンドラインの引数を利用することをお勧めします。

<a id="single-container-bind-mount"></a>

### Rancher サーバーを起動 - シングルコンテナー - MySQLボリュームをバインドでマウント

コンテナ内のデータベースをホスト上のボリュームに保持する場合は、MySQLボリュームをバインドしてRancherサーバーを起動します。

```bash
$ sudo docker run -d -v <host_vol>:/var/lib/mysql --restart=unless-stopped -p 8080:8080 rancher/server
```

このコマンドを使用すると、データベースはホスト上に維持されます。 既存のRancherコンテナがあり、MySQLボリュームのマウントをバインドしたい場合、手順は[アップグレードドキュメント]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/upgrading/#single-container-bind-mount)に記載されています。

<a id="multi-nodes"></a>

### Rancher サーバーの起動 - 完全アクティブ/アクティブ HA

高可用性 (HA) 設定でRancherサーバーを起動するのは、[外部データベースを使用して Rancher サーバーを起動](#using-an-external-database)し、追加のポートを公開し、コマンドに追加の引数をロードバランサー用に追加するのと同じくらい簡単です。

#### HA に関するの追加要件

* HA ノード: * ノード間で開いている必要のあるポート：`9345`
* MySQL データベース * 最小 1 GB RAM * Rancherサーバー 1ノードにつき 50接続 (例えば、ノードが3つの場合は少なくとも150セッション必要です)
* 外部ロードバランサー * 外部ロードバランサーとノードの間で開いている必要があるポート: `8080`

> **注：** 現在、Docker for Windows と Docker for Mac は、Rancher をサポートしていません。

#### 大規模に導入する場合の推奨事項

* それぞれの Rancher サーバーノードは、4GB または 8GB のヒープサイズが必要なので、少なくとも 8GB または 16GB のメモリーが必要です
* MySQL データベースには高速ディスクが必要です
* 本当にHA構成にするには、適切にバックアップしていてレプリケートされているMySQLデータベースを推奨します。 Galera Clusterを使用し、トランザクション・ロックのために単一ノードへの書き込みを強制する方法もあります。

1. HA 構成にセットアップするそれぞれのノードで、次のコマンドを実行します。
  
      bash
       # Launch on each node in your HA cluster
       $ docker run -d --restart=unless-stopped -p 8080:8080 -p 9345:9345 rancher/server \
            --db-host myhost.example.com --db-port 3306 --db-user username --db-pass password --db-name cattle \
            --advertise-address <IP_of_the_Node>
  
  HAセットアップ時には、それぞれのノードで`<IP_of_the_Node>`は個別のIPとして認識されるように一意にします。
  
  If you change `-p 8080:8080` to expose a different port, then you would also add `--advertise-http-port` to match the same port as part of the command.
  
  > **Note:** You can get the help for the commands by running `docker run rancher/server --help`

2. Configure an external load balancer that will balance traffic on ports `80` and `443` across a pool of nodes that will be running Rancher server and target the nodes on port `8080`. Your load balancer must support websockets and forwarded-for headers, in order for Rancher to function properly. See [SSL settings page]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}//installing-rancher/installing-server/basic-ssl-config/) for example configuration settings.

#### Notes on the Rancher Server Nodes in HA

If the IP of your Rancher server node changes, your node will no longer be part of the Rancher HA cluster. You must stop the old Rancher server container using the incorrect IP for `--advertise-address` and start a new Rancher server with the correct IP for `--advertise-address`.

<a id="ldap"></a>

### Enabling Active Directory or OpenLDAP for TLS

In order to enable Active Directory or OpenLDAP for Rancher server with TLS, the Rancher server container will need to be started with the LDAP certificate, provided by your LDAP setup. On the Linux machine that you want to launch Rancher server on, save the certificate.

Start Rancher by bind mounting the volume that has the certificate. The certificate **must** be called `ca.crt` inside the container.

```bash
$ sudo docker run -d --restart=unless-stopped -p 8080:8080 \
  -v /dir_that_contains_the_cert/cert.crt:/ca.crt rancher/server
```

You can check that the `ca.crt` was passed to Rancher server container successfully by checking the logs of the rancher server container.

```bash
$ docker logs <server_container_id>
```

In the beginning of the logs, there will be confirmation that the `ldap.crt` was added correctly.

```bash
DEFAULT_CATTLE_RANCHER_COMPOSE_WINDOWS_URL=https://releases.rancher.com/compose/beta/latest/rancher-compose-windows-386.zip
Adding ca.crt to Certs.
Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d....
done.
done.
[BOOTSTRAP] Starting Cattle
```

<a id="http-proxy"></a>

### Launching Rancher Server behind an HTTP proxy

In order to set up an HTTP proxy, the Docker daemon will need to be modified to point to the proxy. Before starting Rancher server, edit the `/etc/default/docker` file to point to your proxy and restart Docker.

```bash
$ sudo vi /etc/default/docker
```

In the file, edit the `#export http_proxy="http://127.0.0.1:3128/"` to have it point to your proxy. Save your changes and then restart docker. Restarting Docker is different on every OS.

> **Note:** If you are running Docker with systemd, please follow Docker's [instructions](https://docs.docker.com/articles/systemd/#http-proxy) on how to configure the HTTP proxy.

In order for the [Rancher catalog]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/catalog/) to load, the proxy will need to be configured and Rancher server will need to be launched with environment variables to pass in the proxy information.

```bash
$ sudo docker run -d \
    -e http_proxy=<proxyURL> \
    -e https_proxy=<proxyURL> \
    -e no_proxy="localhost,127.0.0.1" \
    -e NO_PROXY="localhost,127.0.0.1" \
    --restart=unless-stopped -p 8080:8080 rancher/server
```

If the [Rancher catalog]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/catalog/) will not be used, run the Rancher server command as you normally would.

When [adding hosts]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/) to Rancher, there is no additional requirements behind an HTTP proxy.