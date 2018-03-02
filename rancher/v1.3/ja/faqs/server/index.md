* * *

title: Rancher サーバーに関する FAQ layout: rancher-default-v1.3 version: v1.3 lang: ja redirect_from: - /rancher/latest/en/faqs/server/

* * *

## ## Rancher サーバーに関する FAQ

### どのバージョンの Rancher が動いているか?

Rancher のバージョンはサイトのフッター部分に記載されています。バージョン番号をクリックすると他のコンポーネントの詳細なバージョン情報を確認できます。

### どのようにプロキシ配下で Rancher を動かすか?

[プロキシ配下に Rancher サーバーをインストール]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/installing-rancher/installing-server/#launching-rancher-server-behind-a-http-proxy) を参照ください。

<a id="server-logs"></a>

### Rancher サーバーコンテナの詳細なログはどこにあるか?

Rancher サーバーが動作しているホストで `docker logs` を実行すると基本的なログを確認できます。 より詳細なログに関しては exec コマンドで Rancher サーバーコンテナ内部にあるログファイルを確認できます。

```bash
# Exec コマンドでサーバーコンテナ内部に入る
$ docker exec -it <container_id> bash

# cattle のログ配置場所へ移動
$ cd /var/lib/cattle/logs/
$ cat cattle-debug.log
```

このフォルダ内には `cattle-debug.log` と `cattle-error.log` が配置されています。 長期間 Rancher サーバーを稼働させている場合、日にち毎に作成されたファイルを参照することができます。

#### Rancher サーバーのログファイルをホスト上にコピー

以下は Rancher サーバーのログをコンテナ上からホスト上にコピーするコマンドになります。

```bash
$ docker cp <container_id>:/var/lib/cattle/logs /local/path
```

### Rancher サーバーの IP を変更するとどうなるか?

Rancher サーバーの IP が変更された場合、更新された情報でホストを再設定する必要があります。

Rancher 上で **管理者** -> **設定** に移動し、**ホスト登録 URL** を Rancher サーバーの新しい URL 情報に置き換えます。 その際、Rancher サーバーを起動した際に公開したポート番号も含めるよう注意してください。 デフォルトでは [インストール手順]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/installing-rancher/installing-server/) に記載されているポート番号 `8080` を利用することが推奨されます。

[ホスト登録 URL]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/settings/#host-registration) を更新後、**インフラストラクチャ** -> **ホストを追加** -> **Custom** に移動していただき、 Rancher エージェントを追加するための `docker run` コマンドが新しい情報で更新されていることを確認してください。 その後、Rancher サーバーの [環境]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/) 内で管理されている全てのホストで更新されたコマンドを実行します。

### なぜ Go-Machine-Service がログを再起動するのか? どのように対処するべきか?

Go-machine-service はウェブソケット経由で Rancher API サーバーに接続するマイクロサービスです。接続に失敗した際には再接続処理を継続します。

もし [単一ノード]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/installing-rancher/installing-server/) セットアップを実施している場合はホスト登録 URL を用いて Rancher API サーバーに接続します。 そのため、Rancher サーバーコンテナの内部からホスト登録 URL に対し接続性があることを確認してください。

```bash
$ docker exec -it <rancher-server_container_id> bash
# Rancher サーバーコンテナの内部より以下を実施
$ curl -i <Host Registration URL you set in UI>/v1
```

成功している場合は JSON の返り値を取得するはずです。認証が有効化されている場合は 401 がレスポンスコードとして返されます。認証が有効化されていない場合は 200 がレスポンスコードとして返されます。

このようにして Rancher API サーバーに対しての接続性を確認します。 また、go-machine-service コンテナにログインし、コンテナに渡されているパラメーターとともに curl コマンドを実行することで確認することもできます。

```bash
$ docker exec -it <go-machine-service_container_id> bash
# go-machine-service コンテナの内部より以下を実施
$ curl -i -u '<value of CATTLE_ACCESS_KEY>:<value of CATTLE_SECRET_KEY>' <value of CATTLE_URL>
```

JSON の返り値とレスポンスコード 200 が取得されるはずです。

curl コマンドが失敗した場合は go-machine-service と Rancher API サーバー間での接続性に問題が発生している可能性があります。

curl コマンドが失敗しなかった場合は一般的な HTTP による接続性ではなく、 go-machine-service がウェブソケットへの接続を確立する際に問題が発生している可能性があります。 go-machine-service と Rancher API サーバー間にプロキシやロードバランサーを配置している場合、ウェブソケットへの接続が許可されるよう設定されているかを見直してください。

### Rancher サーバーは稼働しているが極端に処理が遅い場合、どのように解決できるか?

多くの場合はなんらかの理由でいくつかのタスクがスタックしている可能性があります。 UI 上で **管理者** -> **プロセス** を確認できる場合、どのタスクが `起動中` のままスタックしているか確認することができます。 もし、これらのタスクが長時間に渡り起動中(もしくは失敗)している場合は Rancher がタスクの状態を監視するのに多くのメモリを消費しており、 Rancher サーバーが out of memory 状態を引き起こしている可能性があります。

サーバーが正常に動作するためにはより多くのメモリを割り当てる必要があり、通常は 4GB ほどあれば十分です。

再度 Rancher サーバーを起動するコマンドを実行する際に追加オプションとして `-e JAVA_OPTS="-Xmx4096m"` をコマンドに追加してください。

```bash
$ docker run -d -p 8080:8080 --restart=unless-stopped -e JAVA_OPTS="-Xmx4096m" rancher/server
```

MySQL データベースをどのようにセットアップしているかにもよりますが追加コマンドのために [アップグレード]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/upgrading/) を実施する必要があるかもしれません。

もし、メモリ不足により **管理者** -> **プロセス** タブ自体を確認できない場合はより多くのメモリを割り当てた上で Rancher サーバーを再起動していただき、再度タブからどのプロセスが長時間に渡り稼働中の状態になっているかトラブルシューティングを開始してください。

### Rancher サーバーのデータベースが急速に大きくなる。

Rancher サーバーはデータベースが急激に増大することを防止するためにいくつかのデータベーステーブルを自動的にクリーンアップします。 もし、これらのテーブルのクリーンアップ処理が遅いと感じたら API のデフォルト設定を見直してください。

デフォルトでは `container_event` と `service_event` テーブルのレコードは作成後 2 週間を過ぎると削除されます。 API 上の設定では秒 (`1209600`) で表示され、 設定項目は `events.purge.after.seconds` になります。

デフォルトでは `process_instance` テーブルのレコードは作成後 1 日を過ぎると削除されます。 API 上の設定では秒 (`86400`) で表示され、 設定項目は `process_instance.purge.after.seconds` になります。

これらの設定項目を API から更新するには `http://<rancher-server-ip>:8080/v1/settings` ページにアクセスし、 更新したい設定項目を検索し、`links -> self` のリンクをクリックしてください。 `value` を変更するにはサイドページの `Edit` をクリックしてください。 その時、値は秒で指定することに注意してください。

<a id="databaselock"></a>

### なぜ Rancher サーバーがフリーズしてしまうか? またはアップグレードが失敗するのは?

Rancher を起動した時フリーズ状態のままになってしまった場合、liquibase データベースがロックしている可能性があります。 起動時には liquibase はスキーマの移行処理を行い、 競合状態に陥ると後続の処理を抑制するためにロックエントリーを追加します。

もし、アップグレード処理時であった場合は Rancher サーバーのログに MySQL データベース上のログロックに関する情報が表示され開放されることはありません。

```bash
....liquibase.exception.LockException: Could not acquire change log lock. Currently locked by <container_ID>
```

#### データベースのロックを開放する

> **ノート:** 上記のログロックに関する **exception** を確認できない限りはデータベースのロックを開放しないでください。 アップグレード処理がデータ移行により長時間かかる場合は上記以外の移行に関する問題が発生している可能性があります。

[アップグレードドキュメント]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/upgrading/) に従い Rancher サーバー用にデータコンテナを作成している場合、`DATABASECHANGELOGLOCK` テーブルを更新しロックを削除するために `rancher-data` コンテナに `exec` を実行する必要があります。 データコンテナを作成していない場合はデータベースが動作しているコンテナに対して `exec` を実行してください。

```bash
$ sudo docker exec -it <container_id> mysql
```

MySQL データベースにログインしたら `cattle` データベースにアクセスする必要があります。

```bash
mysql> use cattle;

# ロックされているテーブルを確認する
mysql> select * from DATABASECHANGELOGLOCK;

# コンテナからロックを削除するように更新する
mysql> update DATABASECHANGELOGLOCK set LOCKED="", LOCKGRANTED=null, LOCKEDBY=null where ID=1;


# ロックが削除されたことを確認
mysql> select * from DATABASECHANGELOGLOCK;
+----+--------+-------------+----------+
| ID | LOCKED | LOCKGRANTED | LOCKEDBY |
+----+--------+-------------+----------+
|  1 |        | NULL        | NULL     |
+----+--------+-------------+----------+
1 row in set (0.00 sec)
```