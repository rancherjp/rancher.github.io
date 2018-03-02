* * *

title: Rancher へのコントリビューション layout: rancher-default-v1.3 version: v1.3 lang: ja redirect_from: - /rancher/latest/en/contributing/

* * *

## ## Rancher へのコントリビューション

### 開発

私達の GitHub [リポジトリ](https://github.com/rancher/rancher) 内の wiki に Rancher の開発を始めるための全ての手順が含まれています。

まずは [cowpoke](https://github.com/rancher/rancher/wiki/Cowpoke-1:-Getting-Started-with-Rancher) から開始してみてください!

### リポジトリ

全てのリポジトリは私達のメイン GitHub [ページ](https://github.com/rancher) に配置されています。 そこには Rancher で利用されている多くのリポジトリが存在していますがここでは Rancher で利用されている主要なものに関して詳細を説明したいと思います。

[Rancher リポジトリ](https://github.com/rancher/rancher): このリポジトリは他のリポジトリを統合するためのメインとなるリポジトリです。

[Cattle リポジトリ](https://github.com/rancher/cattle): このリポジトリでは Rancher の各種機能が開発されています。

[UI リポジトリ](https://github.com/rancher/ui): このリポジトリでは Rancher の全ての UI が開発されています。

[Rancher カタログリポジトリ](https://github.com/rancher/rancher-catalog): このリポジトリでは `infra-templates` フォルダ内の [Rancher カタログ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/catalog) 向け [インフラストラクチャサービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/) テンプレートの大部分が含まれており、[環境]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/) の一部分となる [環境テンプレート]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/#what-is-an-environment-template) に利用されます。 私達は追加の [ロードバランサープロバイダー]({{{{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/}}) へのコミュニティコントリビューションを歓迎しています。

[コミュニティカタログ リポジトリ](https://github.com/rancher/community-catalog): このリポジトリでは [Rancher カタログ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/catalog) 向けの全てのコミュニティから寄贈されたテンプレートが含まれます。 私達は Rancher テンプレートに対してのコミュニティコントリビューションを歓迎しています。

[Rancher CLI リポジトリ](https://github.com/rancher/cli): このリポジトリでは [Rancher CLI]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cli/) を開発しています。

[Rancher Compose リポジトリ](https://github.com/rancher/rancher-compose): このリポジトリでは [Rancher Compose]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/rancher-compose/) を開発しています。 また、このリポジトリは docker/libcompose と同期がとられています。

### バグ

もし、バグまたはなんらかの問題を発見した場合は [issue](https://github.com/rancher/rancher/issues/new) を入力し報告してください。 私達は Rancher に関連した多くのリポジトリを管理しており、報告内容を見逃さないようにバグの報告は [Rancher リポジトリ](https://github.com/rancher/rancher)にお願いします。

もし、ドキュメントに関する修正があった場合には [docs リポジトリ](https://github.com/rancher/rancher.github.io) に対して PR を送るか **Edit this page** をクリックしていただき直接ページを修正してください。 <br /> <br />