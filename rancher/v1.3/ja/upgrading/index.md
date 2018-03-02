* * *

title: Rancherのアップグレード layout: rancher-default-v1.3 version: v1.3 lang: ja redirect_from: - /rancher/latest/en/upgrading/

* * *

## ## Rancher サーバーのアップグレード

> **注:** バージョン1.3.x にアップグレードする場合、このアップグレードで期待されることに関しては [バージョン1.3.0](https://github.com/rancher/rancher/releases/tag/v1.3.0) のリリース ノートをご覧ください。

Rancher サーバーをインストールした方法に応じて、アップグレードの手順が異なる場合があります。

* [Rancher サーバー - シングルコンテナー (非-HA)](#single-container)
* [Rancher サーバー - シングルコンテナー (非-HA) - 外部データベース](#single-container-external-database)
* [Rancher サーバー - シングルコンテナー (非-HA) - bind マウントされたMySQLボリューム](#single-container-bind-mount)
* [Rancher サーバー - フルアクティブ / アクティブHA](#multi-nodes)
* [Rancher サーバー - インターネットアクセスなし](#rancher-server-with-no-internet-access)

> **注:**任意の環境変数を設定した場合、また元の Rancher サーバーセットアップで [ldap 証明書]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/installing-rancher/installing-server/#enabling-active-directory-or-openldap-for-tls) を渡した場合は、これらの環境変数や証明書を全て新しくコマンドで追加する必要があります。

### Rancher サーバータグ

Rancher サーバーには2種類のタグがあります。それぞれのメジャーリリースタグ毎に特定のバージョンに対するドキュメントが提供されます。

* `rancher/server:latest` タグは、最新の開発ビルドに対してつけられます。 これらのビルドは、CI自動化フレームワークを通じて検証されています。 これらのリリースは本番環境での開発用ではありません。
* `rancher/server:stable` タグは、最新の安定版リリースです。このタグは、本番環境に推奨するバージョンです。 

末尾に `rc{n}` の付いているリリースは全て使用しないでください。 これらの `rc` ビルドは、 Rancher チームが開発版ビルドをテストするためのものです。

### インフラストラクチャーサービス

Rancher サーバーのアップグレード後、 [インフラストラクチャ サービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/) のアップグレードが可能な場合があります。 Rancher サーバーのアップグレード後、アップグレード可能なスタックがないかインフラストラクチャスタックを確認することを推奨します。 利用可能なアップグレードがある場合は、これらのスタックを一つずつアップグレードしてください。 前のアップグレードを完了させてから次のインフラストラクチャスタックのアップグレードに移行してください。

### Rancher エージェント

各 Rancher エージェントのバージョンは Rancher サーバーのバージョンによって固定されています。 アップグレードを必要とする Rancher サーバーや Rancher エージェントをアップグレードする際は、エージェントは自動的に最新バージョンの Rancher エージェントにアップグレードされます。
<a id="single-container"></a>

### シングルコンテナーのアップグレード (非-HA)

外部DBや bind マウントされたMySQLボリュームを **利用せずに** Rancher サーバーを立ち上げた場合、その Rancher サーバーのデータベースは Rancher サーバーコンテナー内にあります。 データコンテナーを作成するために実行中の Rancher サーバーコンテナーを利用します。 このデータ コンテナーは `--volumes-from` を使用して新たな Rancher サーバーコンテナーを開始するために利用されます。 あるいは、コンテナーからホスト上のディレクトリへデータベースをコピーし、データベースを bind マウントすることができます。

1. コンテナーを停止させます。
    
    ```bash
$ docker stop <container_name_of_original_server>
```

2. `rancher-data` コンテナーを作成します。注: 過去にアップグレードしている `rancher-data` コンテナーが既にある場合は、この手順をスキップできます。
    
    ```bash
$ docker create --volumes-from <container_name_of_original_server> \
--name rancher-data rancher/server:<tag_of_previous_rancher_server>
```

3. Rancher サーバーの最新イメージをプルします。注: この手順をスキップして `latest` イメージを実行しようとすると、アップデートされたイメージは自動的にはプルされません。
    
    ```bash
$ docker pull rancher/server:latest
```

4. `rancher-data` コンテナーからデータベースを利用して新たな Rancher サーバー コンテナーを立ち上げますす。 Rancher 内の全ての変更は `rancher-data` コンテナーに保存されます。 サーバー内でログロックに関する例外が発生した場合は [ログロックを修正する方法]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/faqs/server/#databaselock) を参照してください。
    
    > **注:** Rancher サーバーを保持していた時間に応じて、特定のデータベースの移行に予想以上の時間がかかる場合があります。 次回アップグレードの際にデータベース移行エラーが発生するので、アップグレードを途中で停止しないでください。
    
    ```bash
$ docker run -d --volumes-from rancher-data --restart=unless-stopped \
 -p 8080:8080 rancher/server:latest
```

  


5. 古い Rancher サーバーコンテナーを削除します。 注: `--restart=always` を利用していた場合、コンテナーを停止しただけではマシンが再起動した際にコンテナーが再開されてしまいます。 アップグレードが正常に終了した後、`--restart=unless-stopped` を利用しコンテナーを削除することを推奨します。

<a id="single-container-external-database"></a>

### シングルコンテナーのアップグレード (非-HA) - 外部データベース

外部データベースを利用して Rancher サーバーを立ち上げた場合、同じ [外部DBインストラクション]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/installing-rancher/installing-server/#single-container-external-database) を利用して元の Rancher サーバーコンテナを停止し新たなバージョンの Rancher サーバーを立ち上げることができます。 Rancher サーバーをアップグレードする前に、外部データベースのバックアップを取ることを推奨します。 新しいサーバーをアップし実行した後、古い Rancher サーバーコンテナーを削除することができます。

<a id="single-container-bind-mount"></a>

### シングルコンテナーのアップグレード (非-HA) - bind マウントされたMySQLボリューム

1. 実行中の Rancher サーバーコンテナーを停止します。
    
    ```bash
$ docker stop <container_name_of_original_server>
```

2. データベースファイルをサーバーコンテナー外にコピーします。 注: ホストに格納されているデータベースが既にある場合は、この手順をスキップすることができます。 また、DBがコンテナ外にコピーされる場合、<path>Dockerのコピーの仕様により //mysql/ に格納されます。コンテナ内にボリュームをマウントする場合はこのことを充分に注意してください。バインドでマウントする場合は、mysql/は不要です。
    
    ```bash
$ docker cp <container_name_of_original_server>:/var/lib/mysql <path on host>
```

3. コンテナー内でのmysqlユーザーがmysqlマウントの適切な所有権を得られるようにフォルダへUID/GIDを設定します。
    
    ```bash
$ sudo chown -R 102:105 <path on host>
```

4. 新しいサーバーコンテナーを開始します。
    
    ```bash
$ docker run -d -v <path_on_host>:/var/lib/mysql -p 8080:8080 \
 --restart=unless-stopped rancher/server:latest
```

  


> **注:**以前のコンテナーからデータベースをコピーした場合、ホストパスの末尾に '/' が付いていることが重要です。 そうしなければ最終的にディレクトリが誤った場所になってしまいます。

5. 古い Rancher サーバーコンテナーを削除します。 注: `--restart=always` を利用していた場合、コンテナーを停止しただけではマシンが再起動した際にコンテナーが再開されてしまいます。 アップグレードが正常に終了した後、`--restart=unless-stopped` を利用しコンテナーを削除することを推奨します。

<a id="multi-nodes"></a>

### HA構成のアップグレード

[高可用性 (HA)]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/installing-rancher/installing-server/#multi-nodes) のRancher サーバーを立ち上げた場合、新しいHA構成は引き続き元のHA構成に利用した外部データベースを利用します。

> **注:** HA構成をアップグレードする際、アップグレード中は Rancher サーバー構成が停止します。

1. Rancher サーバーをアップグレードする前に、外部データベースのバックアップを取ることを推奨します。

2. HA構成のそれぞれのノードで、実行中の Rancher コンテナーを停止し削除し、 [Rancher サーバーのインストール]({{site.baseurl}}/installing-rancher/installing-server/#multi-nodes) 時と同じコマンドを用いて新しい Rancher サーバーコンテナーを開始します。ただし、新しい Rancher サーバーのイメージタグを使用します。
    
    ```bash
# On all nodes, stop all Rancher server containers
$ docker stop <container_name_of_original_server>
# Execute the scrip with the latest rancher/server version
$ docker run -d --restart=unless-stopped -p 8080:8080 -p 9345:9345 rancher/server --db-host myhost.example.com --db-port 3306 --db-user username --db-pass password --db-name cattle --advertise-address <IP_of_the_Node>
```

  


> **注:** [HA の古いバージョン]({{site.baseurl}}/rancher/v1.1/{{page.lang}}/installing-rancher/installing-server/multi-nodes/) を実行していたHA構成からアップグレードする場合は、実行中の全ての Rancher HAコンテナーを削除する必要があります。 `$ sudo docker rm -f $(sudo docker ps -a | grep rancher | awk {'print $1'})`

### インターネットアクセスのない Rancher サーバー

インターネット環境の無いユーザーは、アップグレードを成功させるために最新の [インフラストラクチャ サービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/) イメージをダウンロードする必要があります。 最新のデフォルトテンプレート内のイメージが無ければ、インフラストラクチャサービスはアップグレードできません。