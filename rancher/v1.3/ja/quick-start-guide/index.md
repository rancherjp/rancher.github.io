* * *

title: クイックスタートガイド layout: rancher-default-v1.3 version: v1.3 lang: ja redirect_from: - /rancher/quick-start-guide/ - /rancher/latest/en/quick-start-guide/

* * *

## クイック スタート ガイド

このガイドでは、1台のLinuxサーバーに全てインストールして動く、最も簡単なRancherをインストールしてみます。

### Linuxホストを準備する

3.10+ のカーネルが最低限入っている 64 ビット Ubuntu 16.04 と Linux ホストを準備します。 ノートパソコンでも、仮想環境でも、物理サーバーも利用可能です。 Linux ホストには少なくとも**1GB**のメモリがあることを確認してください。 [Docker](https://www.docker.com/)をホストにインストールします。

サーバーにDockerをインストールするには、[Docker](https://docs.docker.com/engine/installation/linux/ubuntulinux/)社の手引きを参照してください。

> **注:**現在、Windows と Mac のDockerはサポートされていません。

### Rancher サーバータグ

Rancher サーバーには2種類のタグがあります。それぞれのメジャーリリースのタグ毎に応じたドキュメントを提供します。

* `rancher/server:latest`タグは、最新の開発ビルドに対してつけられます。 これらのビルドは、CI自動化フレームワークを通じて検証されています。 これらのリリースは本番環境での展開用ではありません。
* `rancher/server:stable` タグは、最新の安定版リリースです。このタグは、本番環境に推奨するバージョンです。 

`rc{n}`サフィックスの付いているリリースは全て使用しないでください。 これらの`rc`ビルドは、Rancherチームが開発版ビルドをテストするためのものです。

### Rancherサーバーを動かす

Rancherサーバーを起動するコマンドは1つだけです。コンテナを起動したら、コンテナのログを調べて、サーバが起動して動いているかを確認します。

```bash
$ sudo docker run -d --restart=unless-stopped -p 8080:8080 rancher/server
# Tail the logs to show Rancher
$ sudo docker logs -f <CONTAINER_ID>
```

Rancherサーバーの起動に数分おまちください。 When the logs show `.... <0>....Startup Succeeded, Listening on port... ` のログが表示されたら、RancherのUIは起動して稼働中です。 ログのこの行が表示されたら、ほとんど設定は終了です。 この出力の後に追加のログが出力される可能性があるので、これが初期起動時のログの最後の行であるとは限りません。

UIは、`8080`ポートで動いているので、表示するには `http://<SERVER_IP>:8080`をブラウザーで開いてください。 Rancherサーバーとブラウザーが同じホストで動いている場合は、`http://192.168.1.100:8080` のように実IPを使ってください。`http://localhost:8080` や `http://127.0.0.1:8080` としないようにしてください。

> **注:**Rancherサーバーにはアクセス制限が設定されておらず、このIPアドレスにアクセスできる誰でもこのUIとAPIを利用できます。 [アクセス制限]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/)を設定することをお勧めします。

### ホストを追加

わかりやすくするためにRancherサーバーを実行しているサーバーをRancherのホストとして追加します。実際の運用では、専用のRancherを実行するホストを動かすことをお勧めします。

ホストを追加するには、UIから **インフラストラクチャー** をクリックして、**ホスト**ページを表示してください。 **ホストを追加**をクリックします。 Rancherで利用するURLを選択するように指示されます。 このURLは、Rancherサーバーが動いているURLになり、これから追加するRancherホストから接続可能なものでなくてはなりません。 この設定は、RancherサーバーがファイヤウォールでNATされたり、ロードバランサーを介してインターネットに公開される場合に便利です。 ホストに`192.168.*.*`のようなプライベートやローカルIPなアドレスがついていた場合、Rancherサーバーにホストが本当にURLにアクセスできるかどうかを尋ねる警告が表示されます。

今のところ、これらの警告は無視してRancherサーバーホスト自体を追加することができます。 **閉じる** をクリックします。 デフォルトでは、Racherエージェントコンテナを起動するDockerコマンドを提供する **Custom** オプションが選択されます。 RancherがDocker Machineを使用してホストを起動するクラウドプロバイダ向けのオプションもあります。

UIでホスト上でオープンにするポートの指示とオプションの設定項目が表示されます。 Rancherサーバーも稼動しているホストを追加するので、このホストで使用するパブリックIPを追加する必要があります。 このオプションで入力されたIPにより、カスタムコマンドでの環境変数が自動的に変更されます。

Rancherサーバーを実行しているホストでこのコマンドを実行します。

Rancher UIで **閉じる**をクリックすると、**インフラストラクチャー** -> **ホスト**表示に戻ります。 数分後にホストが自動的に表示されます。

### インフラストラクチャーサービス

最初にRancherにログインすると自動的に**Default** [環境]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/)になります。 Cattle[環境テンプレート]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/#what-is-an-environment-template)が[インフラストラクチャーサービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/)を起動するときのデフォルト環境として選択されています。 この環境テンプレートでは、[dns]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/dns-service/), [メタデータ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/metadata-service), [ネットワーク]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/networking), and [ヘルスチェック]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/health-checks/)などのようなRancher機能の活用するため起動します。 これらのインフラストラクチャスタックは、**スタック** -> **インフラストラクチャー**にあります。 これらのスタックは、ホストがRancherに完全に追加されるまで、`不健全な`状態です。 ホストを追加した後は、サービスを追加する前にすべてのインフラストラクチャスタックが`active`になるまで待つことをお勧めします。

ホストでは、**システムコンテナを表示**チェックボックスをクリックしない限り、インフラストラクチャサービスのコンテナは非表示になります。

### UIを使用してコンテナを作成する

**スタック**画面に移動して、まだサービスがない場合は、Rancherへようこそ画面の**サービス定義** ボタンをクリックします。 Rancherに既にサービスが存在する場合は、既存のスタックの**サービスを追加**をクリックするか、新しいスタックを作成してサービスを追加できます。 スタックは、サービスをまとめてグループ化する便利な方法です。 新しいスタックを作成する必要がある場合は、 **スタックを追加** をクリックし、名前と説明を入力して **作成** をクリックします。 次に新規スタックで**サービスを追加**をクリックします。

"first-container"のような名前でサービスを追加します。 デフォルトの設定を使用して**Create**をクリックするだけです。 Rancherは、ホスト上でコンテナの起動を開始します。 ホストのIPアドレスにかかわらず、Rancher が `ipsec` インフラストラクチャサービスで管理オーバーレイネットワークを作成したため、***最初のコンテナ***のIPアドレスは `10.42 *.*`の範囲になります。 管理オーバーレイネットワークで、コンテナが異なるホスト間で相互に通信できるように管理しています。

***first-container*** のドロップダウンをクリックすると、コンテナの停止、ログの表示、コンテナコンソールへのアクセスなどの管理アクションを実行できます。

### DockerのCLIを直接使ってコンテナを作成する

Rancherは、コンテナがUIの外で作成されていてもホスト上のコンテナを表示できます。ホストのシェル端末からコンテナを作成してみます。

```bash
$ docker run -d -it --name=second-container ubuntu:14.04.2
```

UI上では、 ***second-container*** がホスト上にポップアップ表示されます。

Rancher は Dockerデーモン 上で発生するイベントを反映し、実際の稼働状況と Rancher での状態を調整します。 [native docker CLI]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/native-docker/)を使用して Rancher を利用する方法の詳細を読むことができます。

***second-container*** のIPアドレスを見ると、`10.42.*.*` の範囲には**ない**ことがわかります。 これは、Docker デーモンによって割り当てられた通常のIPアドレスです。 CLI を使用して Dockerコンテナを作成するとこのようになります。 これによりIPアドレスは Dockerデーモン により割りあてられる通常のIPアドレスになります。 CLIを使用して Dockerコンテナーを作成するとこのようになります。

CLIを使用して Rancher のオバーレイネットワークからIPアドレス取得してDockerコンテナを作成するにはどうすればよいでしょうか？ しなければならないことはコマンドにラベル(すなわち `io.rancher.container.network=true`)を追加するだけです。これにより、このコンテナがRancher の`管理`ネットワークに属しなければならないと認識します。

```bash
$ docker run -d -it --label io.rancher.container.network=true ubuntu:14.04.2
```

### マルチコンテナアプリケーションの作成

個別のコンテナを作成する方法と、ホスト間ネットワークでそれらがどのように接続されるかを説明しました。 しかし、実際のアプリケーションのほとんどは複数のサービスで構成されており、各サービスは複数のコンテナで構成されています。 たとえば、[LetsChat](http://sdelements.github.io/lets-chat/)アプリケーションは、次のようなサービスで構成されます:

  1. ロードバランサー：ロードバランサーはインターネットからのリクエストを" LetsChat" アプリケーションに中継します。
  2. *ウェブ*サービス："LetsChat" コンテナ2つで構成します。
  3. *データーベース*サービス："Mongo" コンテナ1つで構成します。

ロードバランサーは*ウェブ*サービス(すなわち、LetsChat) に接続し、*ウェブ*サービスは*データーベース*サービス(すなわち、Mongo)にリンクします。

このセクションでは、Rancherに [LetsChat](http://sdelements.github.io/lets-chat/) アプリケーションのコンテナを作成して展開する方法を説明します。

**スタック**画面に移動します。まだ、ようこそ画面の場合、その画面を閉じて**サービスを定義する** ボタンをクリックします。 セットアップしたRancherに既にサービスが存在する場合は、**スタックを追加**をクリックして新しいスタックを作成します。 名前と説明を入力して**作成**をクリックします。 次に新しいスタックで**サービスを追加**をクリックします。

まず、`mongo`イメージを使い`データベース`というサービスを作成します。 **作成**をクリックします。 新しく作成された*データーベース*サービスが含まれるスタックページがすぐに表示されます。

次に、**サービスを追加**をクリックして、別のサービスを追加します。 LetsChatサービスと*データーベース*サービスとをリンクをさせます。 `sdelements/lets-chat`イメージを使用して、`web`の名前を付けます。 UI上でスライダを動かして、サービスのスケールをコンテナ2つにします。 **Service Links** に `mongo` という名前の*データベース*サービスを追加します。 Docker の場合と同様に、Rancher は、`mongo`の名前を選択すると、リンクされたデータベースとして`letschat`イメージに必要な環境変数をリンクします。 **作成**をクリックします。

最後にロードバランサーを作成します。 **サービスの追加**ボタンの横にあるドロップダウンメニューアイコンをクリックします。 **ロードバランサーを追加**を選択します。 `letschatapplb`のような名前を入力。 ソースポート(つまり`80`)を入力し、ターゲットサービス(つまり*web*)を選択、そして、ターゲットポート(つまり`8080`) を選択します。 *ウェブ*サービスで`8080番`ポートを使用します。 **作成**をクリックします。

LetsChatアプリケーションが完成しました！ **スタック**画面で、ロードバランサが公開しているポートをリンクとして見つけることができます。 そのリンクをクリックすると、新しいブラウザが開き、LetsChatアプリケーションが表示されます。

### Create a Multi-Container Application using Rancher CLI

In this section, we will show you how to create and deploy the same [LetsChat](http://sdelements.github.io/lets-chat/) application we created in the previous section using our command-line tool called [Rancher CLI]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cli/).

When bringing services up in Rancher, the Rancher CLI tool works similarly to the popular Docker Compose tool. It takes in the same `docker-compose.yml` file and deploys the application on Rancher. You can specify additional attributes in a `rancher-compose.yml` file which extends and overwrites the `docker-compose.yml` file.

In the previous section, we created a LetsChat application with a load balancer. If you had created it in Rancher, you can download the files directly from our UI by selecting **Export Config** from the stack's dropdown menu. The `docker-compose.yml` and `rancher-compose.yml` files would look like this:

#### Example docker-compose.yml

```yaml
version: '2'
services:
  letschatapplb:
    #If you only have 1 host and also created the host in the UI,
    # you may have to change the port exposed on the host.
    ports:
    - 80:80/tcp
    labels:
      io.rancher.container.create_agent: 'true'
      io.rancher.container.agent.role: environmentAdmin
    image: rancher/lb-service-haproxy:v0.4.2
  web:
    labels:
      io.rancher.container.pull_image: always
    tty: true
    image: sdelements/lets-chat
    links:
    - database:mongo
    stdin_open: true
  database:
    labels:
      io.rancher.container.pull_image: always
    tty: true
    image: mongo
    stdin_open: true
```

#### Example rancher-compose.yml

```yaml
version: '2'
services:
  letschatapplb:
    scale: 1
    lb_config:
      certs: []
      port_rules:
      - hostname: ''
        path: ''
        priority: 1
        protocol: http
        service: quickstartguide/web
        source_port: 80
        target_port: 8080
    health_check:
      port: 42
      interval: 2000
      unhealthy_threshold: 3
      healthy_threshold: 2
      response_timeout: 2000
  web:
    scale: 2
  database:
    scale: 1
```

<br /> Download the Rancher CLI binary from the Rancher UI by clicking on **Download CLI**, which is located on the right side of the footer. We provide the ability to download binaries for Windows, Mac, and Linux.

In order for services to be launched in Rancher using Rancher CLI, you will need to set some environment variables. You will need to create an [account API Key]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/api/api-keys/) in the Rancher UI. Click on **API** and click on **Add Account API Key**. Save the username (access key) and password (secret key). Set up the environment variables needed for Rancher CLI: `RANCHER_URL`, `RANCHER_ACCESS_KEY`, and `RANCHER_SECRET_KEY`.

```bash
# Set the url that Rancher is on
$ export RANCHER_URL=http://server_ip:8080/
# Set the access key, i.e. username
$ export RANCHER_ACCESS_KEY=<username_of_key>
# Set the secret key, i.e. password
$ export RANCHER_SECRET_KEY=<password_of_key>
```

<br /> Now, navigate to the directory where you saved `docker-compose.yml` and `rancher-compose.yml` and run the command.

```bash
$ rancher up -d -s NewLetsChatApp
```

<br /> In Rancher, a new stack will be created called **NewLetsChatApp** with all of the services launched in Rancher.