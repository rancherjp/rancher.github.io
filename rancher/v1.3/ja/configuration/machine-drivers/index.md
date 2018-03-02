* * *

title: Rancher におけるマシンドライバー layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## マシンドライバー

[Docker-machine](https://docs.docker.com/machine/) ドライバーを Rancher に追加することができ、Rancher の [ホストを追加]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/other/) から利用することができます。 [管理者]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/#admin) のみがどのマシンドライバーが表示されるかを変更でき、**管理者** -> **マシンドライバー** から変更できます。

**有効** なマシンドライバーのみ **インフラストラクチャ** -> **ホストを追加** ページに表示されます。 デフォルトで Rancher は多くのマシンドライバーを提供していますがいくつかのドライバーのみ **Active** として UI に表示されます。

### マシンドライバーを追加

**マシンドライバーを追加** をクリックすることで簡単にあなた独自のマシンドライバーを追加することができます。

1. **ダウンロード URL** を入力します。この URL には Linux 用 64-bit 向けマシンドライバーを指定します。
2. (オプション) 対象ドライバー向けにカスタマイズされたホスト追加画面を読み込むために **カスタム UI URL** を入力します。 [ui-driver-skel リポジトリ](https://github.com/rancher/ui-driver-skel) により詳細なセットアップ方法が記載されています。
3. (オプション) ダウンロードされたマシンドライバーが予期されたチェックサムと一致するか確認するための **チェックサム** を入力します。
4. 入力が完了したら **作成** をクリックします。

作成をクリック後、Rancher は対象のドライバーを追加し [other hosts]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/other/) の **ドライバー** 項目に対象ドライバーが表示されます。もしくは指定していた場合はカスタムイメージが表示されます。