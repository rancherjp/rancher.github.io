* * *

title: Rancherにおけるインフラストラクチャー サービス layout: rancher-default-v1.3 version: v1.3 lang: jp redirect_from: - /rancher/latest/en/rancher-services/

* * *

## ## インフラストラクチャーサービス

Rancherが開始するとき、それぞれの[環境]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/) は [環境のテンプレート]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/#what-is-an-environment-template) が元になっており、環境のテンプレートによって環境作成時に、インフラストラクチャーサービスが決定します。 それらのインフラストラクチャーサービスには、オーケストレーションの形式、 [外部DNS]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/external-dns-service/)、[ネットワーク]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/networking/)、 [ストレージ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/storage-service/)、 フレームワークサービス ([内部DNS]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/dns-service/)、 [メタデータ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/metadata-service)、 [ヘルスチェック]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/health-checks))が含まれています。

これらのインフラストラクチャーサービスは、[Rancher カタログ](https://github.com/rancher/rancher-catalog) と [コミュニティカタログ](https://github.com/rancher/community-catalog)にある `インフラテンプレートフォルダ` のテンプレート が元になっています。 デフォルトでは、Rancherカタログとコミュニティカタログは有効になっていて、それらは新しい環境のテンプレートを作るときに使用する、基本的なインフラストラクチャーサービス一覧を提供しています。

新しい環境を作るとき、作業用環境のために必要となるインフラストラクチャーサービスのデフォルトセットは、自動的に有効になります。