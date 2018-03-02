* * *

title: Rancher CLI コマンドとオプション layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## コマンドとオプション

Rancher CLI では Rancher 上の環境やホスト、スタック、サービス、コンテナを操作できます。

### Rancher CLI コマンド

| 名前                   | 詳細                                                                            |
| -------------------- | ----------------------------------------------------------------------------- |
| `catalog`            | [カタログに対する操作](#rancher-catalog-reference)                                      |
| `config`             | [クライアント設定のセットアップ](#rancher-config-reference)                                  |
| `docker`             | [ホスト上で docker CLI コマンドを実行](#rancher-docker-reference)                         |
| `environment`, `env` | [環境を操作](#rancher-environment-reference)                                       |
| `events`, `event`    | [リソース変更に関するイベントを表示](#rancher-events-reference)                                |
| `exec`               | [コンテナ上でコマンドを実行](#rancher-exec-reference)                                      |
| `export`             | [スタックに関する YAML 設定ファイルを tar 形式またはローカルファイルとしてエクスポート](#rancher-export-reference) |
| `hosts`, `host`      | [ホストに対する操作](#rancher-hosts-reference)                                         |
| `logs`               | [コンテナのログを表示](#rancher-logs-reference)                                         |
| `ps`                 | [サービス/コンテナを表示](#rancher-ps-reference)                                         |
| `restart`            | [サービス、コンテナを再起動](#rancher-restart-reference)                                   |
| `rm`                 | [サービス、コンテナ、スタック、ホスト、ボリュームを削除](#rancher-rm-reference)                          |
| `run`                | [サービスを起動](#rancher-run-reference)                                             |
| `scale`              | [サービス内で動作するコンテナ数を指定](#rancher-scale-reference)                                |
| `ssh`                | [ホストに対し SSH でアクセス](#rancher-ssh-reference)                                    |
| `stacks`, `stack`    | [スタックに対する操作](#rancher-stacks-reference)                                       |
| `start`, `activate`  | [サービス、コンテナ、ホスト、スタックを起動またはアクティベート](#rancher-startactivate-reference)           |
| `stop`, `deactivate` | [サービス、コンテナ、ホスト、スタックを停止または非アクティブ化](#rancher-stopdeactivate-reference)          |
| `up`                 | [全てのサービスを起動](#rancher-up-reference)                                           |
| `volumes`, `volume`  | [ボリュームに対する操作](#rancher-volumes-reference)                                     |
| `inspect`            | [サービス、コンテナ、ホスト、環境、スタック、ボリュームに関する詳細を表示](#rancher-inspect-reference)            |
| `wait`               | [サービス、コンテナ、ホスト、スタック、マシン、プロジェクトテンプレートに関するリソースを待つ](#rancher-wait-reference)     |
| `help`               | コマンドの一覧または特定コマンドのヘルプ表示                                                        |

<br />

### Rancher CLI グローバルオプション

`rancher` コマンドを利用する際、様々なグローバルオプションが利用できます。

| 名前                                   | 詳細                                                                          |
| ------------------------------------ | --------------------------------------------------------------------------- |
| `--debug`                            | デバッグログを表示                                                                   |
| `--config` value, `-c` value         | クライアント設定ファイルを指定(デフォルトは ${HOME}/.rancher/cli.json)[$RANCHER_CLIENT_CONFIG] |
| `--environment` value, `--env` value | 環境名または ID を指定 [$RANCHER_ENVIRONMENT]                                        |
| `--url` value                        | Rancher API のエンドポイント URL を指定 [$RANCHER_URL]                                 |
| `--access-key` value                 | Rancher API のアクセスキーを指定 [$RANCHER_ACCESS_KEY]                              |
| `--secret-key` value                 | Rancher API の秘密キーを指定 [$RANCHER_SECRET_KEY]                                |
| `--host` value                       | docker コマンドを実行するホストを指定 [$RANCHER_DOCKER_HOST]                             |
| `--wait`, `-w`                       | リソースが利用可能になるまで待つ                                                            |
| `--wait-timeout` value               | 待ち状態のタイムアウト値(デフォルト: 600)                                                    |
| `--wait-state` value                 | 待ち状態の対象(active, healthy 等)                                                  |
| `--help`, `-h`                       | ヘルプを表示                                                                      |
| `--version`, `-v`                    | バージョンを表示                                                                    |

<br />

#### リソースを待つ

これはグローバルフラグであり `--wait` や `-w` のように指定し、コマンドがリソースの状態を待つのに利用されます。 Rancher コマンドを使ってスクリプトを作成する場合に `-w` を利用することで次のコマンドに移る前にリソースが準備完了するまで待つことができます。

デフォルトでは待ち状態のタイムアウトは 10 分になりますがタイムアウトを変更したい場合は `--wait-timeout` を利用することで CLI がエラー終了するまでの時間を変更することができます。

また、`--wait-state` を利用することで終了する直前の特定のリソース状態を定義することもできます。

### Rancher カタログ リファレンス

`rancher catalog` コマンドではカタログテンプレートに関する操作を提供します。

#### オプション

| 名前               | 詳細                                            |
| ---------------- | --------------------------------------------- |
| `--quiet`, `-q`  | ID のみ表示                                       |
| `--format` value | `json` またはカスタムフォーマットによる表示 : {{.Id}} {{.Name}} |
| `--system`, `-s` | システムテンプレートの表示(管理者のみ)                          |

#### サブコマンド

| 名前        | 詳細                     |
| --------- | ---------------------- |
| `ls`      | `カタログテンプレートを一覧表示`      |
| `install` | カタログテンプレートをインストール      |
| `help`    | コマンドの一覧または特定コマンドのヘルプ表示 |

#### Rancher Catalog ls

`rancher catalog ls` コマンドでは環境内の全てのテンプレートを一覧表示します。

##### オプション

| 名前               | 詳細                                            |
| ---------------- | --------------------------------------------- |
| `--quiet`, `-q`  | ID のみ表示                                       |
| `--format` value | `json` またはカスタムフォーマットによる表示 : {{.Id}} {{.Name}} |
| `--system`, `-s` | システムテンプレートの表示(管理者のみ)                          |

<br />

```bash
# 全てのカタログテンプレートを一覧表示
$ rancher catalog ls
# kubernetes が動作している環境下の全てのカタログテンプレートを一覧表示
$ rancher --env k8sEnv catalog ls
# システムテンプレートを一覧表示
$ rancher catalog ls --system
```

#### Rancher Catalog install

`rancher catalog install` コマンドでは環境に対しカタログテンプレートのインストールを実施します。

##### オプション

| 名前                           | 詳細                |
| ---------------------------- | ----------------- |
| `-answers` value, `-a` value | アンサーファイルを指定       |
| `--name` value               | 作成するスタックの名前       |
| `--system`, `-s`             | システムテンプレートをインストール |

<br />

```bash
# カタログをインストール
$ rancher catalog install library/route53:v0.6.0-rancher1 --name route53
# カタログをインストールしてシステムテンプレートとしてラベルを設定
$ rancher catalog install library/route53:v0.6.0-rancher1 --name route53 --system
```

### Rancher コンフィグ リファレンス

`rancher config` コマンドでは [Rancher サーバーに関する設定]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cli/#configuring-the-rancher-command-line-interface) をセットアップします。

```bash
$ rancher config
URL []: http://<server_ip>:8080
Access Key []: <accessKey_of_account_api_key>
Secret Key []:  <secretKey_of_account_api_key>
# もし複数の環境がある場合、
# どの環境を利用するか下記のような質問がされます。
Environments:
[1] Default(1a5)
[2] k8s(1a10)
Select: 1
INFO[0017] Saving config to /Users/<username>/.rancher/cli.json
```

#### オプション

| 名前        | 詳細         |
| --------- | ---------- |
| `--print` | `現在の設定を表示` |

現在の設定情報を参照したい場合は `--print` を指定することができます。

```bash
# 現在の Rancher 設定を参照する
$ rancher config --print
```

### Rancher Docker リファレンス

The `rancher docker` command allows you to run any Docker command on a specific host. Uses the ``$RANCHER_DOCKER_HOST`to run Docker commands. Use`--host <hostid>`or`--host <hostname>` to select a different host.

```bash
$ rancher --host 1h1 docker ps
```

#### オプション

| 名前              | 詳細                     |
| --------------- | ---------------------- |
| `--help-docker` | `docker --help` の結果を表示 |

<br />

> **ノート:** 環境変数 `RANCHER_DOCKER_HOST` が設定されていない場合、`--host` を指定することで対象のホストを特定する必要があります。

### Rancher 環境 リファレンス

`rancher environment` コマンドでは環境に関する操作を提供します。 [アカウント APIキー]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/api/api-keys/#account-api-keys) を利用している場合は環境の作成、更新が行えます。 [環境 APIキー]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/api/api-keys/#environment-api-keys) を利用している場合、他の環境を作成、更新することができず対象の環境のみ参照することができます。

#### オプション

| 名前               | 詳細                                            |
| ---------------- | --------------------------------------------- |
| `--all`, `-a`    | 停止/非アクティブ状態の環境を含む環境を一覧表示                      |
| `--quiet`, `-q`  | ID のみ表示                                       |
| `--format` value | `json` またはカスタムフォーマットによる表示 : {{.Id}} {{.Name}} |

#### サブコマンド

| 名前                      | 詳細                     |
| ----------------------- | ---------------------- |
| `ls`                    | 環境を一覧表示                |
| `create`                | 環境を作成                  |
| `templates`, `template` | 環境テンプレートに関する操作         |
| `rm`                    | 環境を削除                  |
| `deactivate`            | 環境を非アクティブ化             |
| `activate`              | 環境をアクティブ化              |
| `help`                  | コマンドの一覧または特定コマンドのヘルプ表示 |

#### Rancher Env Ls

`rancher env ls` コマンドでは Rancher セットアップ下の全ての環境を一覧表示します。

##### オプション

| 名前               | 詳細                                           |
| ---------------- | -------------------------------------------- |
| `--all`, `-a`    | 停止/非アクティブ状態の環境を含む環境を一覧表示                     |
| `--quiet`, `-q`  | ID のみ表示                                      |
| `--format` value | `json` またはカスタムフォーマットによる表示: {{.Id}} {{.Name}} |

<br />

```bash
$ rancher env ls
ID        NAME      ORCHESTRATION   STATE     CREATED
1a5       Default   Cattle          active    2016-08-15T19:20:46Z
1a6       k8sEnv    Kubernetes      active    2016-08-17T03:25:04Z
# 環境の ID のみ表示
$ rancher env ls -q
1a5
1a6
```

#### Rancher Env Create

`rancher env create` コマンドでは他のオーケストレーション向けの新しい環境を作成します。デフォルトでは cattle がオーケストレーションとして選択されます。

##### オプション

| 名前                             | 詳細                                |
| ------------------------------ | --------------------------------- |
| `--template` value, `-t` value | 作成する環境テンプレートの指定 (デフォルト: "Cattle") |

<br />

```bash
# 環境を作成
$ rancher env create newCattleEnv
# kubernetes 環境を作成
$ rancher env create -t kubernetes newk8sEnv
```

#### Rancher Env Template

`rancher env template` コマンドでは Rancher に対し環境テンプレートのインポートやエクスポート操作を提供します。

##### オプション

| 名前               | 詳細                                           |
| ---------------- | -------------------------------------------- |
| `--all`, `-a`    | 停止/非アクティブ状態の環境を含む環境テンプレートを一覧表示               |
| `--quiet`, `-q`  | ID のみ表示                                      |
| `--format` value | `json` またはカスタムフォーマットによる表示: {{.Id}} {{.Name}} |

##### サブコマンド

| 名前       | 詳細                     |
| -------- | ---------------------- |
| `export` | 環境テンプレートを標準出力にエクスポート   |
| `import` | 環境テンプレートをファイルからインポート   |
| `help`   | コマンドの一覧または特定コマンドのヘルプ表示 |

#### Rancher Env Rm

`rancher env rm` コマンドでは環境の削除操作を提供します。削除には環境名もしくは環境 ID を指定することができます。

```bash
# 名前を指定して環境を削除
$ rancher env rm newk8sEnv
# ID を指定して環境を削除
$ rancher env rm 1a20
```

#### Rancher Env Deactivate

`rancher env deactivate` コマンドでは環境の非アクティブ化操作を提供します。どの環境を更新するか指定するには環境名もしくは環境 ID を指定することができます。

#### Rancher Env Activate

`rancher env activate` コマンドでは環境のアクティブ化操作を提供します。どの環境を更新するか指定するには環境名もしくは環境 ID を指定することができます。

### Rancher イベント リファレンス

`rancher events` コマンドでは Rancher サーバー内で発生した全てのアクティブイベントを一覧表示します。

#### オプション

| 名前                  | 詳細                                           |
| ------------------- | -------------------------------------------- |
| `--format` value    | `json` またはカスタムフォーマットによる表示: {{.Id}} {{.Name}} |
| `--reconnect`, `-r` | エラー時に再接続処理を実施                                |

### Rancher コマンド実行 リファレンス

`rancher exec` コマンドでは Rancher 管理下のコンテナ内で直接コマンドを実行します。 その際、ユーザーはどのホスト上でコンテナが動作しているかは意識する必要が無く、 必要となる情報はコンテナ ID 例) `1i1`, `1i788` など のみとなります。

```bash
# コマンド内でコマンドを実行
$ rancher exec -i -t 1i10
```

#### オプション

| 名前              | 詳細                          |
| --------------- | --------------------------- |
| `--help-docker` | `docker exec --help` の結果を表示 |

`rancher exec` コマンドは対象コンテナを見つけた後、`docker exec` コマンドを対象のホストやコンテナにて実行します。 `docker exec` コマンドのヘルプを参照するには `docker exec --help` の結果を表示するようにオプションを渡します。

```bash
# docker exec --help の結果を表示
$ rancher exec --help-docker
```

### Rancher エクスポート リファレンス

`rancher export` コマンドではスタックに対し `docker-compose.yml` や `rancher-compose.yml` を tar アーカイブとしてエクスポートします。

#### オプション

| 名前                         | 詳細                                                 |
| -------------------------- | -------------------------------------------------- |
| `--file` value, `-f` value | 出力するファイルを指定します。ローカルファイルの代わりに標準出力を指定する場合は - を指定します。 |
| `--system`, `-s`           | システムスタックを含む環境全体のスタックをエクスポート                        |

<br />

```bash
# スタック内の全てのサービスの docker-compose.yml や
# rancher-compose.yml を tar アーカイブとして保存
$ rancher export mystack > files.tar
$ rancher export -f files.tar mystack
```

### Rancher ホスト リファレンス

`rancher hosts` コマンドでは環境下のホストに関する操作を提供します。

#### オプション

| 名前               | 詳細                                           |
| ---------------- | -------------------------------------------- |
| `--all`, `-a`    | 停止/非アクティブ状態の環境を含むホストを一覧表示                    |
| `--quiet`, `-q`  | ID のみ表示                                      |
| `--format` value | `json` またはカスタムフォーマットによる表示: {{.Id}} {{.Name}} |

#### サブコマンド

| 名前       | 詳細       |
| -------- | -------- |
| `ls`     | ホストを一覧表示 |
| `create` | ホストを作成   |

#### Rancher Hosts Ls

`rancher hosts` コマンドでは全てのホストを一覧表示します。

##### オプション

| 名前               | 詳細                                           |
| ---------------- | -------------------------------------------- |
| `--all`, `-a`    | 停止/非アクティブ状態の環境を含むホストを一覧表示                    |
| `--quiet`, `-q`  | ID のみ表示                                      |
| `--format` value | `json` またはカスタムフォーマットによる表示: {{.Id}} {{.Name}} |

<br />

```bash
$ rancher hosts ls
ID        HOSTNAME      STATE     IP
1h1       host-1        active    111.222.333.444
1h2       host-3        active    111.222.333.555
1h3       host-2        active    111.222.333.666
1h4       host-4        active    111.222.333.777
1h5       host-5        active    111.222.333.888
1h6       host-6        active    111.222.333.999
# ホストの ID のみ表示
$ rancher hosts ls -q
1h1       
1h2      
1h3      
1h4       
1h5       
1h6
```

#### Rancher Hosts Create

`rancher hosts create` コマンドでは [ホスト]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/) の作成操作を提供します。 ホストの作成では Docker Machine コマンドが呼び出され Docker Machine ドライバーで必要とされる同一のオプションを渡す必要があります。

### Rancher ログ リファレンス

`rancher logs` コマンドではコンテナ ID またはコンテナ名を指定することで対象コンテナのログを参照できます。

#### オプション

| 名前                   | 詳細                               |
| -------------------- | -------------------------------- |
| `--service`, `-s`    | サービスログを参照                        |
| `--sub-log`          | サービスのサブログを参照                     |
| `--follow`, `-f`     | ログ出力を自動更新する                      |
| `--tail` value       | ログの末尾から何行目まで表示するかを指定(デフォルト: 100) |
| `--since` value      | タイムスタンプによるログ出力                   |
| `--timestamps`, `-t` | タイムスタンプを表示                       |

<br />

```bash
# コンテナ ID を指定して最後 50 行のログを表示
$ rancher logs --tail 50 <ID>
# コンテナ名を指定してログ出力を自動更新
$ rancher logs -f <stackName>/<serviceName>
```

### Rancher PS リファレンス

`rancher ps` コマンドでは Rancher 上の全てのサービス、コンテナを一覧表示します。 `rancher ps` コマンドで特にオプションを指定しない場合、コマンドは環境下の全てのサービスを一覧表示します。

#### オプション

| 名前                   | 詳細                                           |
| -------------------- | -------------------------------------------- |
| `--all`, `-a`        | 停止/非アクティブ状態の環境を含むサービスを一覧表示                   |
| `--system`, `-s`     | システムリソースを表示                                  |
| `--containers`, `-c` | コンテナを表示                                      |
| `--quiet`, `-q`      | ID のみ表示                                      |
| `--format` value     | `json` またはカスタムフォーマットによる表示: {{.Id}} {{.Name}} |

<br />

```bash
# 全てのサービスを一覧表示
$ rancher ps
ID        TYPE      NAME           IMAGE     STATE        SCALE     ENDPOINTS   DETAIL
1s1       service   Default/blog   ghost     activating   3                     Waiting for [instance:Default_blog_3]. Instance status: Storage : Downloading
# サービスの代わりに全てのコンテナを一覧表示
$ rancher ps -c
ID        NAME             IMAGE                           STATE     HOST      DETAIL
1i1       Default_blog_1   ghost                           running   1h1       
1i2       Default_blog_2   ghost                           running   1h2       
1i3       Default_blog_3   ghost                           running   1h3  
```

`detail` 列ではサービスの現在の状態が表示されます。

### Rancher 再起動 リファレンス

`rancher restart` コマンドを利用することで Rancher 管理下のホストまたはサービス、コンテナを再起動することができます。

#### オプション

| 名前                   | 詳細                                |
| -------------------- | --------------------------------- |
| `--type` value       | 再起動するための特定のタイプ(サービス、コンテナ) を指定     |
| `--batch-size` value | 同時に再起動するコンテナ数を指定(デフォルト: 1)        |
| `--interval` value   | 再起動を待つインターバル時間(ミリ秒) (デフォルト: 1000) |

<br />

```bash
# サービス、コンテナ、ホストの ID を指定して再起動
$ rancher restart <ID>
# サービス、コンテナ、ホスト名を指定して再起動
$ rancher restart <stackName>/<serviceName>
```

<br />

> **ノート:** 正しいサービスを参照するためにサービス名には常にスタック名が含まれます。

### Rancher 削除 リファレンス

`rancher rm` コマンドでは Rancher 上のホストやスタック、サービス、コンテナまたはボリュームといったリソースの削除操作が提供されます。

#### オプション

| 名前             | 詳細                        |
| -------------- | ------------------------- |
| `--type` value | 削除するための特定のタイプを指定          |
| `--stop`, `-s` | 削除する前に対象リソースを停止または非アクティブ化 |

<br />

```bash
$ rancher rm <ID>
```

### Rancher 起動 リファレンス

`run` コマンドでは Rancher 上にそれぞれ1コンテナで構成されるサービスをデプロイします。 サービスを作成する際、特定のスタックにサービスを配置したい場合は `--name` を指定し `stackName/serviceName` といった形式で名前を指定することができます。 `--name` を利用しない場合は `Default` スタックに対し docker で指定した名前でサービスがデプロイされます。

```bash
$ rancher run --name App2/app nginx
# CLI は新しく作成されたサービスのサービス ID を返します
1s3
$ rancher -i -t --name serviceA ubuntu:14.04.3
1s4
```

ホストに対しポートを公開する場合はいづれかのホスト上で対象ポートが利用可能であることを確認してください。Rancher は自動的にポートが利用可能であるホストに対しコンテナを配置します。

```bash
$ rancher -p 2368:2368 --name blog ghost
1s5
```

### Rancher スケール リファレンス

デフォルトでは `rancher run` によりサービスを起動した場合、各サービスは1つのコンテナで構成されます。 各サービスのスケールを増やしたい場合は `rancher scale` コマンドを利用できます。 サービスを指定する場合は名前 例) `stackName/serviceName` もしくはサービス ID を利用できます。

```bash
$ rancher scale <stackName>/<serviceName>=5 <serviceID>=3
```

### Rancher ssh リファレンス

`rancher ssh` コマンドでは UI から作成された対象ホストに対しての SSH アクセスが提供され、 [Custom]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/custom/) コマンドで追加されたホストに対しては ssh アクセスはできません。

```bash
$ rancher ssh <hostID>
```

### Rancher スタック リファレンス

`rancher stacks` コマンドでは環境内のスタックに関する操作を提供します。

#### オプション

| 名前               | 詳細                                           |
| ---------------- | -------------------------------------------- |
| `--system`, `-s` | システムリソースを表示                                  |
| `--quiet`, `-q`  | ID のみ表示                                      |
| `--format` value | `json` またはカスタムフォーマットによる表示: {{.Id}} {{.Name}} |

#### コマンド

| 名前       | 詳細        |
| -------- | --------- |
| `ls`     | スタックを一覧表示 |
| `create` | スタックを作成   |

#### Rancher Stacks Ls

`rancher stack ls` コマンドでは選択した環境下のスタックを一覧表示します。

##### オプション

| 名前               | 詳細                                           |
| ---------------- | -------------------------------------------- |
| `--system`, `-s` | システムリソースを表示                                  |
| `--quiet`, `-q`  | ID のみ表示                                      |
| `--format` value | `json` またはカスタムフォーマットによる表示: {{.Id}} {{.Name}} |

<br />

```bash
# 全てのスタックを一覧表示
$ rancher stacks ls
ID        NAME        STATE      CATALOG                           SYSTEM    DETAIL
1e1       zookeeper   healthy    catalog://community:zookeeper:1   false     
1e2       Default     degraded                                     false     
1e3       App1        healthy                                      false     
# スタック ID のみ表示
$ rancher stacks ls -q
1e1
1e2
1e3
```

#### Rancher Stacks Create

`rancher stacks create` コマンドでは新しいスタックを作成します。スタックは空のスタックもしくは `docker-compose.yml` と `rancher-compose.yml` から作成することができます。

##### オプション

| 名前                                    | 詳細                                                    |
| ------------------------------------- | ----------------------------------------------------- |
| `--start`                             | 作成後に起動                                                |
| `--system`, `-s`                      | システムスタックを作成                                           |
| `--empty`, `-e`                       | 空スタックを作成                                              |
| `--quiet`, `-q`                       | ID のみ表示                                               |
| `--docker-compose` value, `-f` value  | Docker Compose ファイルを指定(デフォルト: "docker-compose.yml")   |
| `--rancher-compose` value, `-r` value | Rancher Compose ファイルを指定(デフォルト: "rancher-compose.yml") |

<br />

```bash
# 空スタックを作成
$ rancher stacks create NewStack -e
# docker-compose と rancher-compose ファイルからスタックを作成
# また、作成後にスタックを起動
$ rancher stacks create NewStack -f dc.yml -r rc.yml --start
```

### Rancher 起動/アクティブ化 リファレンス

`rancher start` または `rancher activate` コマンドでは特定のリソースタイプをアクティブ化します 例) ホストまたはサービス、コンテナ 。

#### オプション

| 名前             | 詳細                                    |
| -------------- | ------------------------------------- |
| `--type` value | 起動するための特定のタイプ(サービス、コンテナ、ホスト、スタック) を指定 |

<br />

```bash
# サービス、コンテナ、ホストの ID を指定して起動
$ rancher start <ID>
# サービス、コンテナ、ホスト名を指定して起動
$ rancher start <stackName>/<serviceName>
```

<br />

> **ノート:** 正しいサービスを参照するためにサービス名には常にスタック名が含まれます。

### Rancher 停止/非アクティブ化 リファレンス

`rancher stop` または `rancher deactivate` コマンドでは特定のリソースタイプを非アクティブ化します 例) ホストまたはサービス、コンテナ 。

#### オプション

| 名前             | 詳細                                    |
| -------------- | ------------------------------------- |
| `--type` value | 停止するための特定のタイプ(サービス、コンテナ、ホスト、スタック) を指定 |

<br />

```bash
# サービス、コンテナ、ホストの ID を指定して停止
$ rancher stop <ID>
# サービス、コンテナ、ホスト名を指定して停止
$ rancher stop <stackName>/<serviceName>
```

<br />

> **ノート:** 正しいサービスを参照するためにサービス名には常にスタック名が含まれます。

### Rancher up リファレンス

`rancher up` は docker-compose `up` コマンドに似たコマンドです。

#### オプション

| 名前                                    | 詳細                                                  |
| ------------------------------------- | --------------------------------------------------- |
| `--pull`, `-p`                        | アップグレード前に既にイメージを保持している全ホスト上でイメージの再取得を実施             |
| `-d`                                  | ログ出力によるブロックをしない                                     |
| `--upgrade`, `-u`, `--recreate`       | サービスに変更があった場合にアップグレードを実施                            |
| `--force-upgrade`, `--force-recreate` | サービスの変更の有無に関わらずアップグレードを実施                           |
| `--confirm-upgrade`, `-c`             | アップグレード成功時に自動的に確認を実施し、古いコンテナを削除                     |
| `--rollback`, `-r`                    | 以前起動していたバージョンにロールバック                                |
| `--batch-size` value                  | 同時にアップグレードするコンテナ数を指定(デフォルト: 2)                      |
| `--interval` value                    | 更新のインターバル時間(ミリ秒) (デフォルト: 1000)                      |
| `--rancher-file` value                | Rancher Compose ファイルを指定(デフォルト: rancher-compose.yml) |
| `--env-file` value, `-e` value        | 環境変数を読み込むファイルを指定                                    |
| `--file` value, `-f` value            | Docker Compose ファイルを指定(デフォルト: docker-compose.yml)   |
| `--stack` value, `-s` value           | プロジェクト名を指定(デフォルト: ディレクトリ名)                          |

```bash
# -d オプションによりログ出力によるブロッキングを回避
$ rancher up -s <stackName> -d
```

### Rancher ボリューム リファレンス

`rancher volumes` コマンドでは Rancher 上のボリュームに関する操作を提供します。

#### オプション

| 名前               | 詳細                                           |
| ---------------- | -------------------------------------------- |
| `--all`, `-a`    | 停止/非アクティブ状態の環境を含むボリュームを一覧表示                  |
| `--quiet`, `-q`  | ID のみ表示                                      |
| `--format` value | `json` またはカスタムフォーマットによる表示: {{.Id}} {{.Name}} |

#### コマンド

| 名前       | 詳細         |
| -------- | ---------- |
| `ls`     | ボリュームを一覧表示 |
| `rm`     | ボリュームを削除   |
| `create` | ボリュームを作成   |

#### Rancher Volume LS

`rancher volume ls` コマンドでは環境内の全てのボリュームを一覧表示します。

##### オプション

| 名前               | 詳細                                           |
| ---------------- | -------------------------------------------- |
| `--all`, `-a`    | 停止/非アクティブ状態の環境を含むボリュームを一覧表示                  |
| `--quiet`, `-q`  | ID のみ表示                                      |
| `--format` value | `json` またはカスタムフォーマットによる表示: {{.Id}} {{.Name}} |

<br />

```bash
$ rancher volumes ls
ID        NAME                       STATE      DRIVER        DETAIL
1v1                                  active                   
1v2                                  active                   
1v3                                  detached                 
1v4                                  active                   
1v5                                  detached                 
1v6                                  detached                 
1v7       rancher-agent-state        active     local         
```

#### Rancher Volume Rm

`rancher volume rm` コマンドでは Rancher 上のボリュームを削除します。

```bash
$ rancher volumes rm <VOLUME_ID>
```

#### Rancher Volume Create

`rancher volume create` コマンドでは Rancher 上にボリュームを作成します。

##### オプション

| 名前               | 詳細                     |
| ---------------- | ---------------------- |
| `--driver` value | ボリュームドライバー名を指定         |
| `--opt` value    | 特定ドライバーのキーバリューオプションを指定 |

  


```bash
# Rancher NFS ドライバーに新しいボリュームを作成
$ rancher volume create NewVolume --driver rancher-nfs
```

### Rancher インスペクト リファレンス

`rancher inspect` コマンドではリソースの詳細情報を提供します。

#### オプション

| 名前               | 詳細                                                           |
| ---------------- | ------------------------------------------------------------ |
| `--type` value   | 詳細情報を参照するための特定のタイプ(サービス、コンテナ、ホスト) を指定                        |
| `--links`        | リソース出力や追加アクションのための URL 情報を含む                                 |
| `--format` value | `json` またはカスタムフォーマットによる表示: {{.Id}} {{.Name}} (デフォルト: "json") |

<br />

```bash
# サービス、コンテナ、ホストの ID を指定して詳細情報を参照
$ rancher inspect <ID>
# サービス、コンテナ、ホスト名を指定して詳細情報を参照
$ rancher inspect <stackName>/<serviceName>
```

<br />

> **ノート:** 正しいサービスを参照するためにサービス名には常にスタック名が含まれます。

### Rancher wait リファレンス

`rancher wait` コマンドではリソースに対してのアクションが完了するのを待ちます。 このコマンドは Rancher のコマンドを自動化するのに役立ち、スクリプト内に追加することで対象リソースが次のアクションを実施するために利用可能かどうかを確認することができます。

```bash
$ rancher start 1i1
$ rancher wait 1i1
```