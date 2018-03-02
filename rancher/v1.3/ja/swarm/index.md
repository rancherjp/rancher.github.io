* * *

title: RancherにおけるSwarm layout: rancher-default-v1.3 version: v1.3 lang: jp

* * *

## ## Swarm

RancherでSwarm環境をデプロイするためには、まず最初にコンテナーオーケストレーションとして **Swarm**が設定された[環境のテンプレート]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/#what-is-an-environment-template)から、新しい[環境]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/)を作成する必要があります。

### Swarm環境の作成

環境のドロップダウンから、**環境を管理**をクリックします。 新しい環境を作成するために、**環境を追加**をクリックします。**名前**と**詳細情報** (任意) を設定し、オーケストレーションとしてSwarmがある環境のテンプレートを選択します。 [アクセスコントロール]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/)が有効になっていれば[メンバーを追加]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/#editing-members)でき、追加したメンバーの[ロール]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/#membership-roles)を選択することができます。 リストに追加されたメンバーであれば誰でも、その環境にアクセスできるようになります。

Swarm環境を作成した後は、画面左上の環境のドロップダウンから名前を選択するか、環境の詳細ドロップダウンから**この環境に切り替え**を選択することで、環境に進むことができます。

> **ノート:** Rancherは複数のオーケストレーションフレームワークをサポートしていますが、すでにサービスが稼働している環境を、別の環境に切り替える機能はサポートしていません。

### Swarmの開始

Swarm環境作成後、最低でも１つのホストをその環境に追加すれば[インフラストラクチャーサービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/)がスタートします。 **Swarmサービス** には、最低でも3つのホストを追加する必要があります。 [ホストを追加]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/)する手順は、どのオーケストレーションタイプでも同じです。 最初のホストを追加すれば、Rancherは自動的にSwarmコンポーネント(swarmやswarmエージェント) を含んだインフラストラクチャーサービスのデプロイを開始します。 **Swarm** タブにアクセスすれば、デプロイの状況を確認できます。

> **ノート:** [インフラストラクチャーサービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/)を見ることができるのは、Rancherの管理者か、その環境のオーナーのみです。

#### ShellによるCLI

Rancherは、`docker` や `docker-compose` コマンドを使った、インスタンスに対する簡易なshellアクセスを提供しています。