* * *

title: ネイティブなDockerCLIとRancherの併用 layout: rancher-deault-v1.3 version: v1.3 lang: ja

* * *

## ## ネイティブなDockeCLIとRancherの併用

他のDevOpsツールおよびDockerツールと併用できるように、Rancherはネイティブなdocker CLIとRancherは統合されています。 ここでいう統合とは、高いレベルで述べるとRancherの外でコンテナーを起動、停止を行うと、Rancherはそれらの変更を検知し、変更内容に合わせた情報の更新を行います。

### Dockerのイベントモニタリング

Rancherは全てのホストのDockerイベントをモニタリングすることでリアルタイムなアップデートを実現しています。 Rancherの外側でコンテナーを起動、停止、削除した時（例えば、 `docker stop sad_einstein` をホスト上で実行した時）、Rancherはその変更を検知して、変更内容に合わせて自身の状態を更新します。

> **注意:** 現時点における制約の一つとして、それらの変更検知を行うためのRancherにとって重要なコンテナ群が起動するまで我々は待たなければなりません。 `docker create ubuntu` を実行してもRancherのUIにはコンテナーは表示されませんが、`docker start ubuntu`または`docker run ubuntu`を実行した場合は表示されます。

ホスト上のコマンドラインで`docker events`コマンドを実行することで、Rancherがモニタリングしているものと同じDockerのイベントストリームを見ることができます。

### Dockerのネイティブコマンドで起動したコンテナをRancherのネットワークに所属させる

Rancherの外部で起動したコンテナーであっても、Rancherの管理しているネットワークに参加させることができます。 これによって、Dockerのネイティブコマンドで起動させたコンテナー群であってもホストを跨ったRancherのネットワークに参加させることができます。 この機能を有効にするには、コンテナを作成する際に、`io.rancher.container.network`ラベルを追加し、`true`に設定します。 ここに例を挙げます。

```bash
$ docker run -l io.rancher.container.network=true -itd ubuntu bash
```

Rancherの管理しているネットワークとホストを跨ったネットワーキングについてさらに知りたい場合は、[Rancherにおけるネットワーキング]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/networking/)を参照してください。

### 既存のコンテナのインポート

Rancherはホスト追加時にすでに存在しているコンテナーのインポートもサポートしています。 UIから[カスタムコマンド]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/custom/)を使ってホストを登録した時、ホスト上のコンテナーはRancherによって検知されるとともにインポートされます。

### 定期的な状態の同期

リアルタイムでdockerのイベントをモニタリングすることに加えて、Rancherは定期的にホストと状態を同期します。 ５秒ごとにホストは全てのコンテナの状態についてRancherにレポートし、Rancherの内部で保持している状態と、ホスト上の実際の状態を一致させます。 これによって、ネットワーク帯域の逼迫や、RancherがDockerのイベントを見失うようなサーバの再起動といった事象の発生を予防します。 このような方法で状態を同期させた時、ホスト上のコンテナの状態は実際の状態と常に一致するようになります。 例えば、Rancherがコンテナーが実行中であるとみなしている時に、ホスト上のコンテナーが実際には停止している場合、Rancherはそのコンテナーの状態を停止しているものとしてアップデートする。 Rancherはそのコンテナを再起動させようとはしない。