* * *

title: Quick Start Guide layout: rancher-default-v1.3 version: v1.3 lang: en redirect_from: - /rancher/quick-start-guide/ - /rancher/latest/en/quick-start-guide/

* * *

## クイック スタート ガイド

このガイドでは、1台のLinuxサーバーに全てインストールして動く、最も簡単なRancherをインストールしてみます。

### Linuxホストを準備する

3.10+ のカーネルが最低限入っている 64 ビット Ubuntu 16.04 と Linux ホストを準備します。 ノートパソコンでも、仮想環境でも、物理サーバーも利用可能です。 Linux ホストには少なくとも**1GB**のメモリがあることを確認してください。 [Docker](https://www.docker.com/)をホストにインストールします。

サーバーにDockerをインストールするには、[Docker](https://docs.docker.com/engine/installation/linux/ubuntulinux/)社の手引きを参照してください。

> **注:**現在、Windows と Mac のDockerはサポートされていません。

### Rancher サーバータグ

Rancher サーバーには2種類のタグがあります。それぞれのメジャーリリースのタグ毎に応じたドキュメントを提供します。

* `rancher/server:latest`タグは、最新の開発ビルドに対してつけられます。 These builds will have been validated through our CI automation framework. These releases are not meant for deployment in production.
* `rancher/server:stable` tag will be our latest stable release builds. This tag is the version that we recommend for production. 

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

最初にRancherにログインすると自動的に**Default** [環境]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/)になります。 The default cattle [environment template]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/#what-is-an-environment-template) has been selected for this environment to launch [infrastructure services]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/). These infrastructure services are required to be launched to take advantage of Rancher's benefits like [dns]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/dns-service/), [metadata]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/metadata-service), [networking]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/networking), and [health checks]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/health-checks/). これらのインフラストラクチャスタックは、**スタック** -> **インフラストラクチャー**にあります。 これらのスタックは、ホストがRancherに完全に追加されるまで、`不健全な`状態です。 ホストを追加した後は、サービスを追加する前にすべてのインフラストラクチャスタックが`active`になるまで待つことをお勧めします。

ホストでは、**システムコンテナを表示**チェックボックスをクリックしない限り、インフラストラクチャサービスのコンテナは非表示になります。

### UIを使用してコンテナを作成する

**スタック**画面に移動して、まだサービスがない場合は、Rancherへようこそ画面の**サービス定義** ボタンをクリックします。 Rancherに既にサービスが存在する場合は、既存のスタックの**サービスを追加**をクリックするか、新しいスタックを作成してサービスを追加できます。 スタックは、サービスをまとめてグループ化する便利な方法です。 新しいスタックを作成する必要がある場合は、 **スタックを追加** をクリックし、名前と説明を入力して **作成** をクリックします。 次に新規スタックで**サービスを追加**をクリックします。

"first-container"のような名前でサービスを追加します。 デフォルトの設定を使用して**Create**をクリックするだけです。 Rancher will start launching the container on the host. Regardless what IP address your host has, the ***first-container*** will have an IP address in the `10.42.*.*` range as Rancher has created a managed overlay network with the `ipsec` infrastructure service. This managed overlay network is how containers can communicate with each other across different hosts.

If you click on the dropdown of the ***first-container***, you will be able to perform management actions like stopping the container, viewing the logs, or accessing the container console.

### Create a Container through Native Docker CLI

Rancher will display any containers on the host even if the container is created outside of the UI. Create a container in the host's shell terminal.

```bash
$ docker run -d -it --name=second-container ubuntu:14.04.2
```

In the UI, you will see ***second-container*** pop up on your host!

Rancher reacts to events that happen on the Docker daemon and does the right thing to reconcile its view of the world with reality. You can read more about using Rancher with the [native docker CLI]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/native-docker/).

If you look at the IP address of the ***second-container***, you will notice that it is **not** in the `10.42.*.*` range. It instead has the usual IP address assigned by the Docker daemon. This is the expected behavior of creating a Docker container through the CLI.

What if we want to create a Docker container through CLI and still give it an IP address from Rancher’s overlay network? All we need to do is add a label (i.e. `io.rancher.container.network=true`) in the command to let Rancher know that you want this container to be part of the `managed` network.

```bash
$ docker run -d -it --label io.rancher.container.network=true ubuntu:14.04.2
```

### Create a Multi-Container Application

We have shown you how to create individual containers and explained how they would be connected in our cross-host network. Most real-world applications, however, are made out of multiple services, with each service made up of multiple containers. A [LetsChat](http://sdelements.github.io/lets-chat/) application, for example, could consist of the following services:

  1. A load balancer. The load balancer redirects Internet traffic to the "LetsChat" application.
  2. A *web* service consisting of two "LetsChat" containers.
  3. A *database* service consisting of one "Mongo" container.

The load balancer targets the *web* service (i.e. LetsChat), and the *web* service will link to the *database* service (i.e. Mongo).

In this section, we will walk through how to create and deploy the [LetsChat](http://sdelements.github.io/lets-chat/) application in Rancher.

Navigate to the **Stacks** page, if you see the welcome screen, you can click on the **Define a Service** button in the welcome screen. If there are already services in your Rancher set up, you can click on **Add Stack** to create a new stack. Provide a name and description and click **Create**. Then, click on **Add Service** in the new stack.

First, we'll create a database service called `database` and use the `mongo` image. Click **Create**. You will be immediately brought to a stack page, which will contain the newly created *database* service.

Next, click on **Add Service** again to add another service. We'll add a LetsChat service and link to the *database* service. Let's use the name, `web`, and use the `sdelements/lets-chat` image. In the UI, we'll move the slider to have the scale of the service to be 2 containers. In the **Service Links**, add the *database* service and provide the name `mongo`. Just like in Docker, Rancher will link the necessary environment variables in the `letschat` image from the linked database when you input the "as name" as `mongo`. Click **Create**.

Finally, we'll create our load balancer. Click on the dropdown menu icon next to the **Add Service** button. Select **Add Load Balancer**. Provide a name like `letschatapplb`. Input the source port (i.e. `80`), select the target service (i.e. *web*), and select a target port (i.e. `8080`). The *web* service is listening on port `8080`. Click **Create**.

Our LetsChat application is now complete! On the **Stacks** page, you'll be able to find the exposed port of the load balancer as a link. Click on that link and a new browser will open, which will display the LetsChat application.

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