* * *

title: Packet へのホスト追加 layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## Packet へのホスト追加

Rancher は、 `docker machine` を利用して [Packet](https://www. packet. net/) へのホストのプロビジョニングをサポートします。

### Packet 資格情報の確認

Packetのホストを起動するためには**API キー** が必要です。ご自分のアカウントでPacket へログインします。

1. [Api Keysのページ](https://app. packet. net/portal#/api-keys) に移動します。まだ Api Key を作成していない場合は、新しく作成する必要があります。

2. Api キーの作成画面では Description に説明 (例: Rancher) を入れて、**Generate** をクリックします。

3. 新規作成された **TOKEN** をコピーし Rancher で使用します。

### Packet でホストを起動する

Now that we've found our **Token**, we are ready to launch our Packet host(s). Under the **Infrastructure -> Hosts** tab, click **Add Host**. 次に **Packet** のアイコンを選択します。

1. スライダーで、立ち上げたいホスト数を選択します。
2. **名前**を入力します。また、必要に応じてホストの**説明**を入力します。
3. ご自分のアカウントで作成した **API キー** を入力します。
4. Provide the **Project** that you want the host to be launched. This project is found in your Packet account.
5. Select the **Image**. Whatever `docker machine` supports for Packet is also supported by Rancher.
6. イメージの **サイズ** を選択します。
7. ホストを起動する**リージョン**を選択します。
8. (Optional) Add **[labels]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/#labels)** to hosts to help organize your hosts and to [schedule services/load balancers]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/scheduling/) or to [program external DNS records using an IP other than the host IP]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/external-dns-service/#using-a-specific-ip-for-external-dns).
9. (Optional) Customize your `docker-machine create` command with [Docker engine options](https://docs.docker.com/machine/reference/create/#specifying-configuration-options-for-the-created-docker-engine).
10. When complete, click **Create**.

Once you click on create, Rancher will create the Packet and launch the *rancher-agent* container. In a minute or two, the host will be active and available for [services]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-services/).