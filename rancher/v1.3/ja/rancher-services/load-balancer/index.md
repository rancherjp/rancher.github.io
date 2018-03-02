* * *

title: Rancher におけるロードバランサー layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## ロードバランサー

Rancher は様々なロードバランサードライバーを利用するための機能を提供します。 ロードバランサーは対象サービスに対するルールを追加することで個々のコンテナ向けのネットワークやアプリケーションに対するトラフィックを分散するのに利用されます。 対象サービスは Rancher によりロードバランサーにターゲットとして自動的に登録されコンテナとして動作します。 .

デフォルトでは Rancher は HAproxy を管理ロードバランサーとして利用しており、複数ホスト上に手動でスケールアウトすることができます。 将来的にはさらなるロードバランサープロバイダーを追加し、どのようなロードバランサープロバイダーであっても同一の設定オプションを利用できるよう予定しています。

Cattle 環境におけるロードバランサーの設定オプションに関する詳細は [UI]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-load-balancers/#load-balancer-options-in-the-UI) または [Rancher Compose]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-load-balancers/#load-balancer-options-in-rancher-compose) を、利用例に関しては [UI]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-load-balancers/#adding-a-load-balancer-in-the-ui) または [Rancher COmpose]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-load-balancers/#adding-a-load-balancer-with-rancher-compose) を参照してください。

Kubernetes 環境における詳細情報は [Kubernetes 環境における Rancher 内部ロードバランサーサポート]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/kubernetes/ingress/) を参照してください。