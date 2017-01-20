* * *

title: Rancher概要 layout: rancher-default-v1.3 version: v1.3 lang: ja redirect_from: - / - rancher/ - rancher/latest/ - rancher/latest/en/

* * *

## Rancher の概要

Rancher は組織・企業内での本番運用に耐えうるコンテナ実行環境オープンソースソフトウェアプラットフォームです。 Rancher により組織・企業内でスクラッチからコンテナプラットフォームサービスを作る必要はなくなりました。 Rancher では本番環境でコンテナを管理するフルスタックソフトウェアが提供されます。

Rancherは、主要な4つのコンポーネントから構成されています:

### インフラストラクチャ オーケストレーション

Rancher では、様々なパブリックやプライベートクラウドなどの形態の Linux [ホスト]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/)のコンピューティングリソースを直に利用します。 Linuxホストは、仮想マシン、物理マシンどちらでも構いません。 Rancher では、それぞれのホストにあるCPUやメモリー、ローカルディスクストレージやネットワーク接続以上のものを期待していません。 Rancher側からは、クラウドプロバイダーの仮想マシンであるか、コロケーション設備でホスティングされているベアメタルサーバーであるかを区別できません。

牧場主は、ポータブル電源コンテナー アプリケーションを具体的に設計された [インフラストラクチャ サービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/) 層を実装します。 Rancher インフラストラクチャーサービスには、[ネットワーク]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/networking)、[ストレージ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/storage-service/)、[ロードバランサー]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/load-balancer/)、[DNS]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/dns-service/)とセキュリティが含まれています。 Rancher インフラストラクチャーサービスは、基本的にコンテナーとして実行されます。その為、同じ Rancher インフラストラクチャーサービスはどのクラウド上の Linux ホストでも実行できます。

### コンテナーのオーケストレーションとスケジューリング

多くのユーザーがコンテナ化されたアプリケーションをコンテナオーケストレーションツールやスケジュールフレームワーク内で実行しようとします。 Rancher では[Docker Swarm]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/swarm)や[Kubernetes]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/kubernetes)と[Mesos]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/mesos/)を含む、今人気のコンテナオーケストレーションやスケジュールフレームワークをカバーしています。 一人のユーザーが、複数の Swarm や Kubernetes クラスターを構成できます。 Swarm や Kubernetes ツールを直接操作することもできます。

Swarm、Kubernetes、Mesosに加えて、Rancher では Cattle という独自のコンテナオーケストレーションやスケジュールのフレームワークを持っています。 Cattle は、元々 Docker Swarm を拡張するものとして設計されました。 Docker Swarm の開発が続けられたため、Cattle は Swarm とは違う道を歩み始めました。 Rancherでは、そのため Cattle と Swarm を別々のフレームワークとしてサポートしていきます。 Cattle は、Rancher 自体のインフラストラクチャーサービスをオーケストレーションを拡張したり、Swarm、Kubernetes、Mesosクラスターをアップグレードしたりするために使われます。

### アプリケーション カタログ

Rancher ユーザーは、アプリケーション[カタログ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/catalog)からクリック一つで、マルチコンテナーのクラスター化されたアプリケーションを展開できます。 新しいバージョンのアプリケーションが利用できるようになった時に、ユーザーはアプリケーションをデプロイし、完全自動でアップグレードできます。 Rancher では、Rancher コミュニティの貢献による一般的なアプリケーションからなるパブリックカタログを管理しています。 Rancher ユーザーは、[自分の専用カタログを作成]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/catalog/private-catalog/) できます。

### エンタープライズ レベルのアクセス管理

Rancher ではプラグインによる柔軟なユーザー認証をサポートしており、Active Directory、LDAP、および GitHub を使用した事前に構築されているユーザー[認証の統合]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/)をサポートしています。 Rancher では、[環境]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/)単位のロールベースアクセス制御 (RBAC) をサポートしており、例えば、開発、本番環境へのアクセスをユーザー、グループ単位での許可、拒否をサポートします。

以下の図は、Rancher の主要なコンポーネントと機能を示しています。

<img src="{{site.baseurl}}/img/rancher/rancher_overview_2.png" width="800" alt="Rancher概要" />