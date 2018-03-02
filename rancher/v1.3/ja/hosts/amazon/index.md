* * *

title: Amazon EC2 ホストの追加 layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## Amazon EC2 ホストの追加

Rancher は `docker machine` を利用した [Amazon EC2](http://aws.amazon.com/ec2/) ホストのプロビジョニングをサポートします。

### AWS クレデンシャルの用意

AWS 上にホストを追加する前に、AWS アカウントのクレデンシャル情報とセキュリティグループの情報を用意する必要があります。 **Account Access** 情報は、Amazon の [ドキュメンテーション](http://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSGettingStartedGuide/AWSCredentials.html) を利用して見つけることができます。 **アクセスキーID** と **シークレットキー**の情報は作成時のみ参照可能なため、作成した時点でそれらをどこかに保存しておいてください。

### Amazon EC2 ホストの起動

インフラストラクチャ -> ホストと進み、**ホストを追加** をクリックします。 **Amazon EC2** アイコンを選択します。 対象とする **リージョン** を選択します。 あなたの AWS **アクセスキーID** と **シークレットキー**を入力し、**次へ: 認証とネットワークの選択** をクリックします。 Rancher は AWS でインスタンスを起動する上で利用可能なものをあなたの認証情報を利用して取得します。

インスタンスを作成するアベイラビリティゾーンを選択します。 どのアベイラビリティゾーンを利用するかの選択に基づき、利用可能な VPC ID と サブネット ID が表示されます。 **VPC ID** もしくは **サブネット ID**を選択し、**次へ: セキュリティグループの選択** をクリックします。

次に、ホストに設定するセキュリティグループを選択します。 セキュリティグループには二つの選択肢があります。 **通常** のオプションは既存の `rancher-machine` セキュリティグループを利用もしくは新規作成します。 Rancher が `rancher-machine` セキュリティグループを作成した場合は、Rancher が動作するために必要なポートが全て開放されることになります。 `docker machine` は Docker デーモン用に `2376` 番ポートを自動的に開放します。

**カスタム** オプションでは既存のセキュリティグループを選択することができますが、Rancher が正しく動作するために必要なポート群が間違いなく開放されていることを確認する必要があります。

<a id="EC2Ports"></a>

### Rancher が動作するために必要なポート:

- Rancher サーバー から接続するための TCP `22` 番ポート (Docker のインストールと設定のための SSH 用)
- その他のホストと相互に通信するためのインバウンド/アウトバウンド UDP `500` 番と `4500` 番ポート (IPsec ネットワーキング用)

> **注記:** `rancher-machine` セキュリティグループを再利用する場合に不足しているポートがあっても、再度開放設定が追加されることはありません。 もしホストが正しく起動しない場合、AWS 側でセキュリティグループの内容を確認してください。

セキュリティオプションを選択したら、**次へ: インスタンスオプションの設定** をクリックします。

最後に、ホスト情報の詳細を入力します。

1. 起動したいホスト数をスライダーを利用して選択します。
2. ホストの **名前** を入力し、必要であれば **詳細** も入力します。
3. 起動したい **インスタンスタイプ** を選択します。
4. イメージの **ルートサイズ** を選択します。`docker machine` のデフォルトは16GBで、Rancher も同じ値をデフォルトとしています。
5. (オプション) `docker machine` はデフォルトでは指定されたリージョンの Ubuntu 14.04 LTS イメージを **AMI** として利用します。 独自の AMI を選択することもできます。 独自の AMI を入力する場合、その AMI が選択したリージョンで利用可能かどうかを確認してください。
6. (オプション) インスタンスプロファイルとして利用する **IAM プロファイル** を入力します。
7. (オプション) ホストの整理、[サービス/ロードバランサーのスケジューリング]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/scheduling/)、[ホスト IP ではない IP の外部 DNS レコードへの設定]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/external-dns-service/#using-a-specific-ip-for-external-dns) などのために、**[ラベル]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/#labels)** を追加します。
8. (オプション) **拡張オプション** では、`docker-machine create` コマンドを [Docker エンジンのオプション](https://docs.docker.com/machine/reference/create/#specifying-configuration-options-for-the-created-docker-engine) によってカスタマイズすることができます。
9. 完了したら、**作成** をクリックします。

Rancher は EC2 インスタンスを作成し、インスタンス内で *rancher-agent* コンテナを起動します。 ホストは数分でアクティブになり、[サービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-services/) で利用できるようになります。