* * *

title: Rancherにおけるネットワーキング layout: ranccher-default-v1.3 version: v1.3 lang: ja redirect_from: - /rancher/latest/en/rancher-services/networking/

* * *

## ## ネットワーキング

Rancherでは[CNI](https://github.com/containernetworking/cni)フレームワークを実装することで、Rancher内部で異なるネットワークドライバーを利用することができます。 CNIフレームワークを最大限活用するために、[環境]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments)に、**ネットワークサービス**インフラストラクチャサービスがデプロイされている必要があります。 デフォルトでは、全ての[環境テンプレート]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/#what-is-an-environment-template)で、**ネットワークサービス**は有効化されています。

**ネットワークサービス**インフラストラクチャサービスでは、サービスで利用したいネットワーキングのタイプを選択することができます。 デフォルトの環境テンプレートでは、**IPsec**ネットワークドライバーを利用することで、IPsecトンネリングを利用したシンプルでセキュアなオーバレイネットワークを構成しています。

環境にネットワークドライバがデプロイされると、デフォルトネットワークが自動的に作成されます。 `managed`ネットワークを利用しているサービスはいずれも、デフォルトのネットワークを利用していることになります。 デフォルトでは、GUIまたはCLIを使って立ち上げられた全てのサービスは`managed`ネットワークを利用しています。 `managed`ネットワークの他にもサービスを起動する際には、ネットワークドライバの名前ベースで利用するネットワークを直接指定することができます。

Docker CLIから直接立ち上げられたコンテナでは、`--label io.rancher.container.network=true`を追加のラベルとして指定することで、`managed`ネットワークを利用させることができます。 このラベルを指定しない場合は、DockerCLIから立ち上げられたコンテナーは`host`ネットワークを利用するようになります。

> **注意:**ネットワークドライバ(たとえば`managed`)から立ち上げられたネットワークに依存しているいずれのコンテナも、ネットワークインフラストラクチャサービス(たとえば`ipsec`)が削除された場合、ネットワーク処理が失敗するようになります。

ロードバランサーやDNSサービスといったRancherの特徴の大部分は、サービスが`managed`ネットワークに含まれていることを要件としていますが、特定のネットワークドライバでないと動作しないといった依存関係は存在しません。

RancherのIPsecネットワーキングを利用する際、コンテナーはデフォルトのdocker0ブリッジ上のDockerブリッジのIP(172.17.0.0/16) とRancherの管理するIP(10.42.0.0/16) を割り当てられます。 同一の環境内にあるコンテナーはmanagedネットワークを経由してルーティングされ、パケット到達性が確保されます。

> **注意:**Rancherによって管理されているIPアドレスは、Dockerのinspectコマンドので得られるようなDockerメタデータには表示されません。これは、ときおり、DockerブリッジのIPアドレスを要求する特定のツールとの非互換性を引き起こします。 将来のDockerのバージョンでは、より明確な形でオーバーレイネットワークを取り扱うことができるように、Dockerコミュニティーと共同で改善活動を進めています。

もし、ホストを跨った通信で問題に直面した場合は、トラブルシューティングのための[文書]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/faqs/troubleshooting/#cross-host-communication)を参照してください。

### RnacherのIPsecネットワークサービスの例

CNIフレームワークを最大限活用するために、ネットワークインフラストラクチャサービスを有効化することができます。このインフラストラクチャサービスはyamlファイルないのネットワークドライバから生成されます。 yamlファイルの`network_driver`キーには、定義可能なオプションがいくつかあります。

RancherのIPsercインフラストラクチャサービスの例は次の通りです。`network_driver`は`rancher-compose.yml`ファイルで設定されます。

```yaml
ipsec:
  network_driver:
    name: Rancher IPsec
    default_network:
      name: ipsec
      host_ports: true
      subnets:
      - network_address: '10.42.0.0/16'
      dns:
      - 169.254.169.250
      dns_search:
      - rancher.internal
    cni_config:
      '10-rancher.conf':
        name: rancher-cni-network
        type: rancher-bridge
        bridge: docker0
        bridgeSubnet: '10.42.0.0/16'
        isDefaultGateway: true
        hairpinMode": true
        hostNat: true
        hairpinMode: true
        linkMTUOverhead: 98
        ipam:
          type: rancher-cni-ipam
          logToFile: /var/log/rancher-ipam.log
```

#### デフォルトのネットワーク

`default_network`では、環境におけるネットワーキングのオプションを定義します。

##### Name

サービスのネットワークモードを選択する際に利用されるデフォルトのネットワーク名です。

#### Host Ports

デフォルトでは、ポートとホストを公開することは許可されているが、ポートやホストを公開することが許可されていないネットワークを構築することもできます。

#### Subnets

管理されているオーバレイネットワークのサブネットのネットワークアドレスを選択することができます。

#### DNSとDNS検索

DNSとDNS Searchの値は、コンテナに自動的に設定されます。

### CNI configuration

ネットワークドライバーに対して、`cni_config`内でCNI設定を行うことができます。RancherのIPsecインフラストラクチャサービスのCNI設定例は上に記述のある通りです。

### Network Metadata

ネットワークドライバのメタデータによってRancherで作成されるネットワークのメタデータは設定されます。