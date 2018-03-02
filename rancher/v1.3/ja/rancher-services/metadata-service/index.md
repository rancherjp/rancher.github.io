* * *

title: Rancherにおけるメタデータサービス layout: rancher-default-v1.3 version: v1.3 lang: ja redirect_from: - /rancher/rancher-services/metadata-service/

* * *

## ## メタデータサービス

Rancherはメタデータインフラストラクチャサービスを通じてサービスとコンテナのデータを提供しています。 メタデータサービスの形でHTTPベースのAPIとして提供されており、動作中のDockerインスタンスの管理にこのメタデータを活用することができます。 これらのデータはDockerコンテナやRancherのサービスといった静的な情報、同じサービス内部における対向となるコンテナについてのディスカバリ情報といった実行時データ(動的な情報) を含めることができます。

Rancherのメタデータサービスを用いることで、Rancherによって管理されているネットワークを使ってあらゆるコンテナーにアクセスすることができ、Rancher内部のコンテナについての情報を収集することができます。 メタデータはコンテナーやコンテナーによって構成されるスタックやサービス、またはコンテナが載っているホストに関連しています。 メタデータはJSONフォーマットの形で提供されます。

Rancherによって管理されているネットワークないでいくつかの方法を用いて、コンテナーは立ち上がります。 ランチャーにおけるネットワーキングについての詳細は、Rancherにおける[ネットワーキング]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/networking)の動作について参照してください。

### メタデータの取得方法

Rancher UIから、コンテナーのドロップダウンメニュー内の**シェルを実行**を選択することで、コンテナーのシェルに入ることができます。 ドロップダウンメニューはコンテナの上をホバーすることで表示されます。

メタデータを取得するには以下のcurlコマンドを実行してください。

```bash
# もしcurlがインストールされていない場合は、インストールしてください。
$ apt-get install curl
# プレーンテキストのレスポンスを取得するための基本的なcurlコマンド
$ curl http://rancher-metadata/<version>/<path>
```

レスポンスフォーマットと同様に、どのメタデータを収集したいかによってパスは変わります。

| メタデータ              | パス                          | 内容                                                                                                                                                                                                                                                      |
| ------------------ | --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| コンテナー              | `self/container`            | コマンドを実行しているコンテナー上のメタデータを提供します。                                                                                                                                                                                                                          |
| コンテナーが構成しているサービス   | `self/service`              | コマンドを実行しているコンテナーのサービスのメタデータを提供します。                                                                                                                                                                                                                      |
| コンテナーが構成しているスタック   | `self/stack`                | コマンドを実行しているコンテナーのスタックを提供します。                                                                                                                                                                                                                            |
| コンテナーがデプロイされているホスト | `self/host`                 | コマンドを実行しているコンテナーのホスト上のメタデータを提供します。                                                                                                                                                                                                                      |
| 他のコンテナー            | `containers`                | 全てのコンテナーのメタデータを提供します。 プレーンテキストでは、全てのコンテナーのインデックスされたレスポンスを提供します。 JSONフォーマットの場合は、全てのコンテナーの全メタデータを提供します。 インデックス番号か、コンテナーの名前をパスに含めることで、特定のコンテナーのメタデータを取得することができます。                                                                                          |
| 他のサービス             | `services`                  | 全てのサービスのメタデータを提供します。 プレーンテキストでは、全てのサービスのインデックスされたレスポンスを提供します。 JSONフォーマットでは、全サービスの全てのメタデータを提供します。 インデックス番号か、サービスの名前をパスに含めることで、特定のサービスのメタデータを取得することができます。 コンテナーまでドリルダウンした場合、V1(`2015-07-25`)では、コンテナーの名前だけが返されますが、V2(`2015-12-19`)ではコンテナーオブジェクトが返却されます。    |
| 他のスタック             | `stacks/<stack-name>` | 全てのスタックのメタデータを提供します。 プレーンテキストでは、全てのスタックのインデックスされたレスポンスを提供します。 JSONフォーマットでは、全スタックの全てのメタデータを提供します。 インデックス番号か、スタックの名前をパスに含めることで、特定のスタックのメタデータを取得することができます。 コンテナーの詳細までドリルダウンした場合、V1(`2015-07-25`)では、コンテナーの名前だけが返されますが、V2(`2015-12-19`)ではコンテナーオブジェクトが返却されます。 |

### メタデータのバージョニング

`curl`コマンドでは、特定のバージョンを指定して利用することを強く推奨します。指定しない場合は、`最新`のものが選択されます。

> **注意:**`最新`のバージョンには変更が加えられるので、返却されるデータはどのリリースでも変更される可能性があり、その結果としてあなたの作成したコードと互換性がなくなる可能性があります。

メタデータサービスのバージョンは公開日に準拠しています。

| バージョンリファレンス | バージョン      |
| ----------- | ---------- |
| V2          | 2015-12-19 |
| V1          | 2015-07-25 |

#### バージョン間での差異

##### V1 対 V2

`/services/<service-name>/containers`または`/stacks/<stack-name>/services/<service-name>/containers`で終わるHTTPのパスを使ってコンテナーにドリルダウンした時、V1ではコンテナ名を返しますが、V2ではコンテナオブジェクトが返されます。 V2のメタデータサービスで追加の情報が提供されます。

##### 例

Rancherでは、3コンテナで構成される`barservice`と呼ばれるサービスを含む`foostack`と呼ばれるスタックがあります。

```bash
# V1を使うとサービスを構成するコンテナーの名前だけが返されます。
$ curl --header 'Accept: application/json' 'http://rancher-metadata/2015-07-25/services/barservice/containers'
["foostack_barservice_1", "foostack_barservice_2", "foostack_barservice_1"]

# V2を使うとサービスを構成するコンテナーオブジェクトが返されます。
$ curl --header 'Accept: application/json' 'http://rancher-metadata/2015-12-19/services/barservice/containers'
[{"create_index":1, "health_state":null,"host_uuid":...
...
# サービスを構成する全てのコンテナーのメタデータのリスト
# V2を利用すると、特定のコンテナーオブジェクトへとドリルダウンすることができます。
$ curl --header 'Accept: application/json' 'http://rancher-metadata/2015-12-19/services/barservice/containers/foostack_barservice_1'
[{"create_index":1, "health_state":null,"host_uuid":...
...
# サービスを構成する全てコンテナーのメタデータ
# /stacks/<service-name>を利用することで、サービスやコンテナへとドリルダウンすることができます。

# V1を使うとサービスを構成するコンテナーの名前だけが返されます。
$ curl --header 'Accept: application/json' 'http://rancher-metadata/2015-07-25/stacks/foostack/services/barservice/containers'
["foostack_barservice_1", "foostack_barservice_2", "foostack_barservice_1"]

# V2を使うとサービスを構成するコンテナーオブジェクトが返されます。
$ curl --header 'Accept: application/json' 'http://rancher-metadata/2015-12-19/stacks/foostack/services/barservice/containers'
[{"create_index":1, "health_state":null,"host_uuid":...
...
# サービスを構成する全てのコンテナーの全メタデータのリスト
...}]
```

### プレーンテキスト 対 JSON

メタデータはプレーンテキストまたはJSONフォーマットのどちらかで取得することができます。どのようにメタデータを利用したいかによって、いずれかのフォーマットを選択できます。

#### プレーンテキスト

curlコマンドを実行した時は、リクエストされたパスに対するプレーンテキスト返ってきます。 パスのトップレベルからスタートして、利用可能なキーを追加してパス階層を深くしていくことを繰り返すことで、目的とするメタデータを取得することができます。

```bash
$ curl 'http://rancher-metadata/2015-12-19/self/container'
create_index
dns/
dns_search/
external_id
health_check_hosts/
health_state
host_uuid
hostname
ips/
labels/
memory_reservation
milli_cpu_reservation
name
network_from_container_uuid
network_uuid
ports/
primary_ip
primary_mac_address
service_index
service_name
stack_name
stack_uuid
start_count
state
system
uuid
$ curl 'http://rancher-metadata/2015-12-19/self/container/name'
# 注意: curlは改行は提供しないので、全て一行で表示されます。
Default_Example_1$root@<container_id>
$ curl 'http://rancher-metadata/2015-12-19/self/container/label/io.rancher.stack.name'
Default$root@<container_id>
# インデックス、または名前のどちらかを指定することで配列から値を取得することができます。
# Arrays can use either the index or name to go get the values
$ curl 'http://rancher-metadata/2015-12-19/services'
0=Example
# パスとしてインデックス、または名前のどちらでも指定することができます。
$ curl 'http://rancher-metadata/2015-12-19/services/0'
$ curl 'http://rancher-metadata/2015-12-19/services/Example'
```

#### JSON

curlコマンドでは、HTTPヘッダに`Accept:application/json`を指定することで、JSONフォーマットで値を取得することができます。

```bash
$ curl --header 'Accept: application/json' 'http://rancher-metadata/2015-12-19/self/container'
$ curl --header 'Accept: application/json' 'http://rancher-metadata/2015-12-19/self/stack'
# スタック内の他サービスのメタデータを取得します。
$ curl --header 'Accept: application/json' 'http://rancher-metadata/2015-12-19/services/<service-name>'
```

### メタデータのフィールド

#### コンテナー

| フィールド                         | 内容                                                                                                                                                                                                         |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `create_index`                | サービス内で起動されているコンテナーの順番を含みます。 例えば、2はサービス内で２つ目のコンテナとして起動したものであることを表します。 注意: create_indexは再利用されることは決してありません。 2コンテナーで構成されるサービスがあり、2番目のコンテナーを削除した場合、次に起動するコンテナーは、たとえサービスに2つのコンテナしかない場合でも`create_index`として3を持ちます。 |
| `dns`                         | コンテナーのDNSサーバー                                                                                                                                                                                              |
| `dns_search`                  | コンテナーのサーチドメイン                                                                                                                                                                                              |
| `external_id`                 | ホスト上のDockerコンテナーのID                                                                                                                                                                                        |
| `health_check_hosts`          | コンテナーが健全性チェックを実行しているコンテナーのホストUUIDのリスト                                                                                                                                                                      |
| `health_state`                | [ヘルスチェック]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/health-checks/)が有効化されている場合のコンテナーの健全性の状態                                                                                           |
| `host_uuid`                   | Rancherサーバーがホストに割り当てるユニークなホスト識別子                                                                                                                                                                           |
| `hostname`                    | コンテナーのホスト名                                                                                                                                                                                                 |
| `ips`                         | 複数のNICがサポートされている場合は、IPのリストになります。                                                                                                                                                                           |
| `labels`                      | [コンテナーのラベル]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/scheduling/#labels)のリストです。ラベルのフォーマットは`key`:`value`の形で記述されます。                                                                    |
| `memory_reservation`          | コンテナーが利用可能なメモリ量のソフトリミット                                                                                                                                                                                    |
| `milli_cpu_reservation`       | コンテナーが利用可能なCPU割合のソフトリミット。整数で指定し、1あたり単一のCPUの1/1000を表す。                                                                                                                                                      |
| `name`                        | コンテナーの名前                                                                                                                                                                                                   |
| `network_from_container_uuid` | どのネットワークからきているコンテナーのUUIDなのかを表す。                                                                                                                                                                            |
| `network_uuid`                | Rancherがネットワークに対して割り当てるユニークなネットワーク識別子                                                                                                                                                                      |
| `ports`                       | [コンテナーで利用されているポート]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-services/#port-mapping)のリスト ポートのフォーマットは`hostIP:publicIP:privateIP[/protocol]`となっています。                             |
| `primary_ip`                  | コンテナーのIPアドレス                                                                                                                                                                                               |
| `primary_mac_address`         | コンテナーのプライマリーMACアドレス                                                                                                                                                                                        |
| `service_index`               | The last number in the container name of the service                                                                                                                                                       |
| `service_name`                | サービスの名前                                                                                                                                                                                                    |
| `stack_name`                  | サービスが所属しているスタック名                                                                                                                                                                                           |
| `stack_uuid`                  | Rancherがスタックに付与しているユニークなスタック識別子                                                                                                                                                                            |
| `start_count`                 | コンテナーの起動回数                                                                                                                                                                                                 |
| `state`                       | コンテナーの状態                                                                                                                                                                                                   |
| `system`                      | [インフラストラクチャサービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/)に所属するコンテナか否かを表します。                                                                                              |
| `uuid`                        | Rancherがコンテナーに対して割り当てるユニークなコンテナ識別子                                                                                                                                                                         |

#### サービス

| フィールド                  | 内容                                                                                                                                                                                                  |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `containers`           | サービスに含まれるコンテナーの一覧                                                                                                                                                                                   |
| `create_index`         | サービスで最後に作成されたコンテナーのcreate_indexの値 注意: create_indexが再利用されることは決してありません。 2つのコンテナーから構成されるサービスにおいて2つ目のコンテナーを削除した場合、create_indexは2になります。 そのサービスにおいて次のコンテナーが立ち上がった際には、コンテナーは2つしかないが、create_indexは3となる。 |
| `expose`               | The ports that are exposed on the host without being published on the host.                                                                                                                         |
| `external_ips`         | [外部サービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-external-services/)の外部IPのリスト                                                                                         |
| `fqdn`                 | サービスのFqdn                                                                                                                                                                                           |
| `health_check`         | サービス上の[健全性チェックの設定]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/health-checks/)                                                                                                   |
| `hostname`             | [外部サービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-external-services/)のCNAME                                                                                            |
| `kind`                 | Rancherサービスのタイプ                                                                                                                                                                                     |
| `labels`               | [サービスにおけるラベル]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/scheduling/#labels)のリスト。ラベルのフォーマットは`key:value`です。                                                                      |
| `lb_config`            | [ロードバランサ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-load-balancers/)の設定                                                                                                 |
| `links`                | リンクされたサービスのリストです。 リンクのフォーマットは`stack_name/service_name:service_alias`となっています。 `links`では全てのキー(例えば、全てのリンクに対する`stack_name/service_name`)を表示します。`service_alias`を取得するには、特定のキーへとドリルダウンする必要があります。           |
| `metadata`             | [ユーザの追加したメタデータ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/metadata-service/#adding-user-metadata-to-a-service)                                                       |
| `name`                 | サービス名                                                                                                                                                                                               |
| `ports`                | サービスで利用されているポート ポートのフォーマットは`hostIP:publicIP:privateIP[/protocol]`です。                                                                                                                                |
| `primary_service_name` | Sidekicksが存在する場合の主たるサービスの名前です。                                                                                                                                                                      |
| `scale`                | サービスのスケール                                                                                                                                                                                           |
| `sidekicks`            | [sidekicks]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-services/#sidekick-services)のサービス名のリスト                                                                            |
| `stack_name`           | サービスからなるスタックの名前                                                                                                                                                                                     |
| `stack_uuid`           | Rancherがスタックに割り当てているユニークなスタック識別子                                                                                                                                                                    |
| `system`               | サービスが[インフラストラクチャサービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/)か否か                                                                                                 |
| `uuid`                 | Rancherがサービスに割り当てているユニークなサービス識別子                                                                                                                                                                    |

#### スタック

| フィールド              | 内容                                                                                                  |
| ------------------ | --------------------------------------------------------------------------------------------------- |
| `environment_name` | スタックが所属している[Environment]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/)の名前  |
| `environment_uuid` | Rancherがスタックに割り当てているユニークなスタック識別子                                                                    |
| `name`             | [スタック]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/stacks/)の名前                   |
| `services`         | スタックに含まれるサービスのリスト                                                                                   |
| `system`           | スタックが[インフラストラクチャサービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/)か否か |
| `uuid`             | Rancherがスタックに割り当てるユニークなスタックの識別子                                                                     |

#### ホスト

| フィールド              | 内容                                                                                                                 |
| ------------------ | ------------------------------------------------------------------------------------------------------------------ |
| `agent_ip`         | RancherエージェントのIPアドレス、たとえば、`CATTLE_AGENT_IP`環境変数                                                                    |
| `hostname`         | [ホスト]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/)の名前                                           |
| `labels`           | [ホストラベル]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/#host-labels)のリスト。ラベルのフォーマットは`key:value`です。 |
| `local_storage_mb` | ホストに載っているストレージの容量をMBで表します。                                                                                         |
| `memory`           | ホストに載っているメモリをMBで表します。                                                                                              |
| `milli_cpu`        | ホストに載っているCPUの量。1000が1つのCPUに相当する。                                                                                   |
| `name`             | [ホスト]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/)の名前                                           |
| `uuid`             | Rancherがホストに割り当てるユニークなホスト識別子                                                                                       |

### サービスにユーザーのメタデータを追加する

Rancherではサービスに対してユーザがメタデータを付与することができます。 現時点では、[Rancher Compose]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/rancher-compose/)を通じてのみサポートされています。メタデータは`rancher-compose.yml`ファイルに記述します。 `metadata`キーでメタデータを記述することでyamlがメタデータサービスで利用されているJSONフォーマットにパースされます。

#### ` rancher-compose.yml`の例

```yaml
service:
  # サービスのスケール
  scale: 3
  # ユーザの追加したメタデータ
  metadata:
    example:
      name: hello
      value: world
    example2:
      foo: bar

```

サービスが起動した後、メタデータサービスから`.../self/service/metadata`または、`.../services/<service_id>/metadata`といったパスを指定することでメタデータを取得できます。

#### JSONクエリ

```bash
$ curl --header 'Accept: application/json' 'http://rancher-metadata/latest/self/service/metadata'
{"example":{"name":"hello","value":"world"},"example2":{"foo":"bar"}}

```

#### プレーンテキストクエリ

```bash
$ curl 'http://rancher-metadata/latest/self/service/metadata'
example/
$ curl 'http://rancher-metadata/latest/self/service/metadata/example'
name
value
$ curl 'http://rancher-metadata/latest/self/service/metadata/example/name'
# 注意: curlでは改行は含まれないため、全ての値は単一の行でまとめて表示されます。
hello$root@<container_id>
```