* * *

title: Rancher の設定 layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## 設定

Rancher の **管理者** -> **設定** ページでは様々なカスタマイズが可能です。

### ホスト登録

ホストを追加する前にホストの登録情報を入力するよう促されます。 この登録セットアップではどのように Rancher サーバーがホストと通信するかが設定されます。 既に [アクセスコントロール]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control) が設定されている場合、URL は既にアクセス可能な状態であるため設定を促すメッセージは表示されません。

セットアップではホストが Rancher API と通信するためのベース URL を決定します。 デフォルトでは Rancher が現在アクセスしている UI をベース URL として選択します。 アドレスを変更する場合は、Rancher API と通信するためのポート番号も指定することに注意してください。 また、Rancher で SSL を有効化している場合はプロトコルも `https` に変更するようにしてください。 このセットアップでは [カスタムホストを追加]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/custom/) する際にどのようなコマンドを生成するかに影響します。

Rancher で [アクセスコントロール]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/) を有効化している場合、**管理者** のみがホスト登録を変更することができます。 アクセスコントロールが設定されている場合、デフォルトでは最初の**管理者**が最初のユーザーとなり アクセスコントロールがまだ設定されていない場合、サイトにアクセスできる全てのユーザーがホスト登録を変更できます。 この設定は **管理者** -> **ホスト登録 URL** タブから変更することが可能です。

### カタログ

デフォルトでは [カタログ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/catalog/) は3つのカタログが有効化されています。

* [Rancher インフラストラクチャ](https://github.com/rancher/infra-catalog) には全てのインフラストラクチャサービスに関するテンプレートが含まれています。
* [Rancher 公式ライブラリ](https://github.com/rancher/rancher-catalog) には Rancher で保証されたアプリケーションに関するテンプレートが含まれています。
* [コミュニティライブラリ](https://github.com/rancher/community-catalog) にはコミュニティにより寄贈されたアプリケーションに関するテンプレートが含まれています。

設定ページは [管理者]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/accounts/#admin) のみ利用可能なため、管理者のみ Rancher にプライベートカタログを追加することができます。 カタログの追加は簡単でカタログ名と git URL を追加するのみです。 git URL の正しいフォーマットは [こちら](https://git-scm.com/docs/git-clone#_git_urls_a_id_urls_a) を参照してください。 追加されたカタログはすぐにカタログタブから利用することができます。

追加のプライベートカタログを作成したい場合は git リポジトリを [特定のフォーマット]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/catalog/private-catalog) で設定する必要があります。

### 統計情報

デフォルトでは Rancher は設定情報に関する匿名の統計情報を収集してもよいか質問します。 このデータはユーザーの傾向をより理解し、どのようにして Rancher をより良くすることができるかのために利用されます。 詳細は [Rancher におけるテレメトリ]({{site.baseurl}}/rancher/telemetry/) を参照してください。