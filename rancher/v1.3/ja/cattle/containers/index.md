* * *

title: Containers layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## コンテナ

### コンテナを追加

通常、コンテナを追加するためには[サービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-ui/applications/stacks/adding-services)を使うことを推奨しています。これはユーザーに利便性を提供するためです。しかし、時にはコンテナを1つだけ立ち上げたいという要望があることも理解しています。

**インフラストラクチャ** -> **コンテナ** ページで、**コンテナを追加**をクリックしてください。 コンテナを作成するときに、`docker run` コマンドがサポートするすべてのオプションは、Rancherでもサポートしています。

1. **名前**を指定して、必要であればコンテナの **詳細情報**を設定してください。
2. 使用する**イメージ**を指定してください。 [DockerHub](https://hub.docker.com/)やRancherに追加した[レジストリ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/registries)の任意のイメージを使用できます。 イメージ名の構文は`docker run`コマンドと一緒です。
    
    イメージ名の構文について、デフォルトではdocker registryからpullします。タグの指定がない場合、latestタグがついたイメージをpullします。
    
    `[registry-name]/[namespace]/[imagename]:[version]`
    
    <a id="port-mapping"></a>

3. 必要であればポートマップを設定してください。そうすることでホストのIPアドレスを通してコンテナが公開したポートにアクセスできるようになります。 **ポートマップ**セクションでは、コンテナとの通信に使用するパブリックホストポートを定義します。 また、コンテナのパブリックホストポートに接続するプライベートコンテナポートも定義します。
    
    **ランダムポートマッピング**
    
    Rancherのランダムポートマッピングを使用したいのであれば、パブリックホストポートを空白にして、プライベートコンテナポートを定義する必要があります。 プライベートコンテナポートは通常、コンテナの公開ポートの中の一つです。
    
    > **ノート:** ポートがRancherで公開されているとき、`docker ps` ではポートが表示されません。Rancherがポートを動的に扱うためにiptableで管理しているからです。

4. 様々なタブで、Dockerで使用できるオプションはRancherでも使用できます。デフォルトのオプションは、`-i -t` です。
    
    もしコンテナを **インフラストラクチャ** -> **コンテナ** ページから追加した場合、Rancherはホストを自動的に割り当てます。 それ以外の場合は、コンテナを追加するホストを選択すると、ホストとは**セキュリティ/ホスト**タブに表示されます。
    
    スケジューリングルールを適用することもできますし、コンテナにラベルを付ける機能もあります。 ラベルとスケジューリングに関する詳細は [ここ]({{{{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-ui/scheduling/)を参照してください。

5. コンテナのオプションを記入し終わったら、 **作成**をクリックして下さい。

## ## コンテナの作成

From the dropdown of a container, you can select different actions to perform on a container. When viewing containers on a host or service, the dropdown icon can be found by hovering over the container name. In the **Infrastructure** -> **Containers**, the dropdown icon is only visible for containers that were created specifically on the hosts. Any containers created through a service will not display its dropdown icon.

You can always click on the container name, which will bring you to the container details page. On that page, the dropdown menu is located in the upper right hand corner next to the state of the container.

When you select **Edit** from the dropdown menu, you will be only able to change the name and description of the container. Docker containers are immutable (not changeable) after creation. The only things you can edit are things that we store that aren't really part of the Docker container. This includes restarting, it's still the same container if you stop and start it. You will need to remove and recreate a container to change anything else.

> **Note:** When ports are exposed in Rancher, it will not show up in `docker ps` as Rancher manages the iptable rules to make the ports fully dynamic.

You can **Clone**, which will pre-fill the **Add Container** screen with all the settings from an existing container. If you forget one thing, you can clone the container, change it, and then delete the old container.

### コンテナの状態を変更する

When a container is in a **Running** state, you can **Stop** the container. This will stop the container on the host, but will not remove it. After the container is in the *Stopped* state, you can select **Start** to have the container start running again. Another option is to **Restart** the container, which will stop and start the container in one step.

You can **Delete** a container and have it removed from the host.

### シェルを実行

When you select **Execute Shell**, it brings you into the container shell prompt.

### ログを見る

It's always useful to see what is going on with the logs of a container. Clicking **View Logs** provides the equivalent of `docker logs <CONTAINER_ID>` on the host.