* * *

title: Rancher エージェント/ホスト に関する FAQ layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## Rancher エージェント/ホスト に関する FAQ

### プロキシ越しにあるホストを設定する方法は？

プロキシ越しにあるホストを認識するためには、Docker デーモンの設定先を該当のプロキシに変更する必要があります。 詳細な構成は、[adding custom host page]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/custom/#hosts-behind-a-proxy) に記載しています。

### Rancher エージェントが起動に失敗するときの理由は？

#### `--name rancher-agent` を追加している

UIから取得した ` docker run  rancher/agent ... ` コマンドに、 ` --name rancher-agent ` を追加すると Rancher エージェントは起動に失敗します。 Rancher エージェントは、初回起動後に3種類のコンテナを起動し、 実行中のコンテナが１つと、停止中のコンテナが2つの状態になります。 Rancher サーバーに正常に接続するためには、 `rancher-agent` と `rancher-agent-state` という名前のコンテナが必要です。 Docker によってデフォルトの名前を付けられた3番目のコンテナは削除可能です。

#### 複製した VM を使っている

VM を複製し、複製した VM を登録しようとすると、動作せずに Rancher エージェントのログに次のようなエラーを吐き出します。 `ERROR: Please re-register this agent.` Racnher が `/var/lib/rancher/state` に保存した一意のIDが複製したVMと同じになり、再登録することはできません。

この問題を解決するためには、複製したVMで以下のコマンドを実行します。`rm -rf /var/lib/rancher/state; docker rm -fv rancher-agent; docker rm -fv rancher-agent-state`、一度実行すれば登録できるようになります。

<a id="agent-logs"></a>

### Rancher エージェントコンテナの詳細なログの場所は？

v1.3.0に関しては、`docker logs`を実行すれば Rancher エージェントコンテナのエージェントに関するすべてのログを得ることができます。

### ホストの登録が正しいかチェックする方法は？

Rancher エージェントから Rancher サーバへの接続に問題がある場合は、ホストの設定を確認してください。 Rancher UI にてホストを追加しようとする時、ホスト登録URL を設定する必要があります。 その URL は、ホストと Rancher API サーバーとの接続を確立するために使われます。 この URL は、ホストから到達可能でなければなりません。 到達できることを確認するためには、ホストにログオンして curl コマンドを実行します。

    curl -i <UI で設定したホスト登録 URL >/v1
    

成功している場合は JSON の返り値を取得するはずです。認証が有効化されている場合は 401 がレスポンスコードとして返されます。認証が有効化されていない場合は 200 がレスポンスコードとして返されます。

> **ノート:**通常の HTTP 接続と web ソケットでの接続(ws://)両方を使えます。 URL がプロキシやロードバランサーを指定している場合は、web ソケットでの接続を使うように設定されていることを確認してください。

### ホストはどのようにして IP アドレスを決定しているのですか？変更することはできますか？再起動などでホストの IP アドレスが変わった場合にすることはありますか？

エージェントが Rancher サーバーに接続すると、エージェントの IP アドレスは自動検出されます。 時には、あなたが望んでいない IP アドレスになったり、Docker のブリッジで使われる IP アドレス`（172.17.xxx.xxx）`になることがあります。

また、既に登録済みのホストがあり、そのホストの再起動後に IP アドレスが新しくなった場合は、UI にあるホストの IP アドレスとは一致しなくなります。

そういう時は、`CATTLE_AGENT_IP` の設定を上書きして、必要な IP アドレスに設定することができます。

ホストの IP アドレスが正しくないと、コンテナーは管理ネットワークに接続できなくなります。 ホストとコンテナーを管理ネットワークに入れるには、新しい IP アドレスを環境変数として指定して、[ Custom ホストの追加（Step4） ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/custom/)の コマンドを編集します。 編集したコマンドをホストで実行してください。 既存のエージェントの停止や削除はしないでください。

```bash
$ sudo docker run -d -e CATTLE_AGENT_IP=<NEW_HOST_IP> --privileged \
    -v /var/run/docker.sock:/var/run/docker.sock \
    rancher/agent:v0.8.2 http://SERVER_IP:8080/v1/scripts/xxxx
```

### Rancher の管理外でホストを削除するとどうなりますか？

もしホストが Rancher の管理外で削除されると、Rancher サーバーは そのホストが一覧から削除されるまで表示し続けます。 これらのホストは、`Disconnected` 状態になる前に、まず最初に *Reconnecting* 状態になります。 UI からホストの削除操作を行うことで、これらのホストを削除することができます。

`Disconnected` のホストにデプロイしたサービスがある場合、 [ヘルスチェック]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/health-checks/)を追加した場合のみ、他のホストで再展開します。

### 同じホストが UI に何度も表示されるのはどうしてですか？

ホストの追加に boot2docker を使っている場合、ホストは `var/lib/rancher` を永続化して持つことができません。ここは Rancher がホストを識別するために必要な情報を保存するために使う場所です。