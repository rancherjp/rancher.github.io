* * *

title: Rancher における監査ログ layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## 監査ログ

[管理者]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/#admin) のみが監査ログにアクセスすることができます。 監査ログは **管理者** -> **監査ログ** から参照することができます。

Rancher の監査ログは異なるイベントタイプをまとめたものになります。

* `api` プレフィックスから始まるものは API コールになります。イベントタイプは API 関連のアクションであり、誰がアクションを引き起こしたか、どのように API がコールされたか(UI から呼び出された、API キーから呼び出されたなど)が記録されています。
* それ以外の `api` プレフィックス **ではない** イベントは Rancher サーバーが実施したイベントになります。 例えばサービスのコンテナを調整する際にインスタンスが作成された場合は `instance.create` イベントとして記録されます。