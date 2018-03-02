* * *

title: ロードバランサー layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## ロードバランサー

Rancher では、様々な種類のロードバランサードライバーを使用することができます。 ロードバランサーは対象サービスに対するルールを追加することで個々のコンテナ向けのネットワークやアプリケーションに対するトラフィックを分散するのに利用されます。 対象サービスは Rancher によりロードバランサーにターゲットとして自動的に登録されコンテナとして動作します。 Rancher を使えば簡単にロードバランサーをスタックに追加できます。

デフォルトでは Rancher は HAproxy を管理ロードバランサーとして利用しており、複数ホスト上に手動でスケールアウトすることができます。 このドキュメントの残りの例としてロードバランサーの様々なオプションを紹介しますが、特に HAProxy ロードバランサーサービスについて記載していきます。 将来的にはさらなるロードバランサープロバイダーを追加し、どのようなロードバランサープロバイダーであっても同一の設定オプションを利用できるよう予定しています。 [UI](#load-balancer-options-in-the-UI) と [Rancher Compose](#load-balancer-options-in-rancher-compose) のロードバランサーオプションを確認し [UI]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-load-balancers/#adding-a-load-balancer-in-the-ui) と [Rancher Compose]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-load-balancers/#adding-a-load-balancer-with-rancher-compose) を使った例を紹介して行きます。

### UI でのロードバランサー追加

[サービスの追加]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-services/#adding-services-in-the-ui) で作成した "letschat" アプリケーションにロードバランサーをセットアップする方法を説明します。

まずはロードバランサーの作成から始めます。"サービスの追加"の隣のドロップダウンアイコンから **ロードバランサーの追加** をクリックします。 デフォルトのスケールでは、1つのコンテナーです。 名前を入力してください（例：LetsChatLB）。

ポートルールではデフォルトの`パブリック`アクセス、デフォルトの`http`プロトコルにし、ソースポートを`80`、"letschat" サービスを選択し、ターゲットポートには`8080`を使用します。 **作成**をクリックしてください。

では、ロードーバランサーの動きを見てください。 スタック画面では、ロードバランサーのソースポートとして使った `80` 番ポートにリンクがあることが分かります。 そのリンクをクリックすると、自動的にロードバランサーが起動しているホストの中の1つを指し示す形で、ブラウザの新しいタブが立ち上がります。 リクエストは"LetsChat" コンテナーの1つにリダイレクトされます。 画面をリフレッシュすると、ロードバランサーは新しいリクエストを他の"letschat"サービスのコンテナーにリダイレクトします。 デフォルトでは、対象サービスへのトラフィックを振り分けるためにラウンドロビンアルゴリズムを使います。 アルゴリズムは[カスタム HAProxy configuration](#custom-haproxy-configuration) でカスタムできます。

### UIでのロードバランサーオプション

Rancher は HAproxy を内包したコンテナで出来た、トラフィックをサービスに振り分けるためのロードバランサーを提供します。

> **ノート:** ロードバランサーは管理ネットワーク上でのみ動作します。 もし他のネットワークを選択すると、ロードバランサーとして動作しなくなります。

ロードバランサーを追加するには**サービスを追加**のアイコンをクリックした後に出てくるドロップダウンの中から **ロードバランサーを追加**を選択します。

スライダーを使ってロードバランサーのコンテナーの数といったスケールを選択することができます。 もしくは、**常に全てのホスト上でこのコンテナを1インスタンスとしてさせる**事も選択できます。 このオプションで、ロードバランサーは[環境]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/)に追加されたすべてのホスト上でスケールすることになります。 スケジューリングルールを**スケジューリング**で設定した場合は、Rancher はスケジューリングルールに沿った形でのみコンテナーを起動します。 環境にホストを追加しても、スケジューリングルールに合致していなければ、そのホストでコンテナは起動しません。

> **ノート:** その環境におけるロードバランサーの数はホストの数を上回ることは出来ません。そうしないと port の競合が起きロードバランサーサービスが起動中の状態のままになってしまいます。 ロードバランサーのスケールを編集するか、[ホストを追加]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/) するまで使用可能なホストを探し port を開放しようとし続けます。

**名前**は入力する必要があり、必要であればロードバランサーの**説明**を入力してください。

次に、ロードバランサーのポートルールを定義してください。 作成できるポートルールは2種類あります。 既にあるサービスを対象にするサービスルールと、セレクターに設けた基準を対象とするセレクタールールがあります。

サービスルールとセレクタールールを作る場合、ホストとパスルールはUIで表示されている順に上から下へ評価されます。

#### サービスルール

サービスルールは Rancher 内の既存サービスを対象としたポートルールです。

**アクセス** のセクションでは、このロードバランサーのポートを公開する(つまり、ホストの外側からアクセスできるという事)か、内部の環境からのみアクセスできるようにするかを決めます。 デフォルトでは、Rancher はポートをパブリックにすることを前提にしますが、`内部` を選択するとポートは同一環境内のサービスからしかアクセスできなくなります。

**プロトコル** を選びます。 詳細は [protocol options](#protocol) を参照してください。 SSL終端の処理が必要なプロトコル（つまり `https` または `tls`）を選択する場合、** SSL終端処理 ** タブで証明書を追加します。

次に、トラフィックの送信元として、**リクエストホスト**、**ソースポート**、**パス**を指定します。

> **ノート:** `42` 番ポートはロードバランサーのソースポートとして使用できません。 Rancher がこのポートを [ヘルスチェック]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/health-checks)に使用するためです。

##### リクエストホスト/パス

リクエストホストは各サービス固有のHTTPホストヘッダーにします。 リクエストパスは固有のパスにします。 リクエストホストとパスは個別に設定でき、組み合わせることで特有のリクエストを作成することができます。

###### 例:

    domain1.com -> Service1
    domain2.com -> Service2
    
    domain3.com -> Service1
    domain3.com/admin -> Service2
    

###### ワイルドカード

Rancher はホストベースルーティングの追加において、ワイルドカードをサポートしています。以下のワイルドカード構文をサポートしています。

    *.domain.com -> hdr_end(host) -i .domain.com
    domain.com.* -> hdr_beg(host) -i domain.com.
    

##### 対象のサービスとポート

各サービスに対して、トラフィックを向かわせるサービスを**対象**で指定します。 サービスのリストはその環境内にあるすべてのサービスが元になっています。 サービスと共に、トラフィックを向かわせる**ポート**を選択します。 サービスのこのプライベートポートは通常、コンテナーイメージで公開されているポートです。

#### セレクタールール

セレクタールールでは、特定のサービスを対象にする代わりに、[セレクターの値]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/labels/#selector-labels)を指定します。 セレクターはサービスのラベルに基づいて、対象のサービスを取得するために使います。 ロードバランサーが作られると、セレクタールールは対象のサービスがあるかどうか、環境内の既存サービスを評価します。 サービスの追加またはラベルの変更を行うと、そのサービスが対象のサービスになるかどうか、セレクターの値と比較されます。

各**ソースポート**に対して、**リクエストホスト**や**パス**を追加できます。 [セレクターの値]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/labels/#selector-labels)は **対象**で指定し、 トラフィックを向かわせる特定の**ポート** を指定します。 サービスのこのプライベートポートは通常、コンテナーイメージで公開されているポートです。

##### 例: 2つのセレクタールール

1. ソースポート: `100`; セレクター: `foo=bar`; ポート: `80`
2. ソースポート: `200`; セレクター: `foo1=bar1`; ポート: `80`

* サービスAは`foo=bar`ラベルを持っていて、最初のセレクタールールに一致します。`100`番ポートに来るどんなトラフィックもサービスAに向かいます。
* サービスBは`foo1=bar1`ラベルを持っていて、2番目のセレクタールールに一致します。`200`番ポートに来るどんなトラフィックもサービスBに向かいます。
* サービスCは `foo=bar` と `foo1=bar1` ラベルを持っていて、両方のセレクタールールに一致します。どちらのソースポートからのトラフィックもサービスCに向かいます。

> **ノート:**現在、一つのセレクターソースポートルールに対して複数のホスト/パスを使いたい場合、[Rancher Compose](#selector) を使って対象サービスのホスト/パスを設定する必要があります。

#### SSL終端処理

**SSL終端処理** タブで、`https` と `tls` プロトコルを使うための証明書を追加することが出来ます。 **証明書**のドロップダウンで、ロードバランサーのメインの証明書を選択できます。

Rancher に証明書を追加するには、 [**インフラストラクチャ**タブの証明書の追加方法]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/certificates/)を参照してください。

要求されたホスト名に基づいてクライアントに適切な証明書を見せるために、ロードバランサーに複数の証明書を設定することは可能です ( [Server Name Indication](https://en.wikipedia.org/wiki/Server_Name_Indication)を参照してください)。 SNIをサポートしていない古いクライアントでは、動かないかもしれません。（これらはメインの証明書を取得してしまいます。） 現代のクライアントは、リストの中からマッチする証明書が提供され、マッチする証明書がない場合はメインの証明書が提供されます。

#### ロードバランサーのスティッキーポリシー

ロードバランサーで**スティッキー**を選択できます。スティッキーはWEBサイトのcookieを使う場合に使いたいcookieでのポリシーです。

Rancher は2つのオプションをサポートしています

* **None**: これはcookieによるポリシーがないことを意味します。
* **新しいcookieを作成**: このオプションは、アプリケーションの外部でcookieが定義されることを意味します。 このcookieはロードバランサーがリクエストとレスポンスに設定するものです。 このcookieでスティッキーポリシーを決定します。

#### HAProxyのconfigをカスタム

Rancher はHAProxyのロードバランサーを使っているため、ロードバランサーのHAProxy設定をカスタムすることができます。 このセクションで定義されたものは、Rancher が生成する設定に追加されます。

##### HAProxyのconfigをカスタムした例

    global
        maxconn 4096
        maxpipes 1024
    
    defaults
        log global
        mode    tcp
        option  tcplog
    
    frontend 80
        balance leastconn
    
    frontend 90
        balance roundrobin
    
    backend mystack_foo
        cookie my_cookie insert indirect nocache postonly
        server $IP <server parameters>
    
    backend customUUID
        server $IP <server parameters>
    

#### ロードバランサーのラベル/スケジューリング

ロードバランサーにラベルを追加する機能とロードバランサーが起動する場所をスケジューリングする機能を提供しています。 ラベルとスケジューリングの詳細に関しては [here]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/scheduling/#scheduling-options-in-the-ui)を参照してください.

### Rancher Compose でのロードバランサーの追加

前述の[サービスの追加]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-services/#adding-services-with-rancher-compose)にて作成した”letschat”アプリケーションにロードバランサーを設定する方法を説明します。

詳細については [Rancher Composeの設定]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/rancher-compose/)を参照してください。

> **ノート**: この例では、 `<version>` をロードバランサーのイメージタグに使用します。 Rancher の各バージョンでは、ロードバランサーとしてサポートしている `lb-service-haproxy` の固有のバージョンがそれぞれあります。

UIの例で使ったのと同じ例を設定します。 始めるには、 `docker-compose.yml` ファイルと `rancher-compose.yml` ファイルの作成が必要です。 Rancher Composeで、ロードバランサーを起動できます。

#### `docker-compose.yml` の例

```yaml
version: '2'
services:
  letschatlb:
    ports:
    - 80
    image: rancher/lb-service-haproxy:<version>
```

#### `rancher-compose.yml` の例

```yaml
version: '2'
services:
  letschatlb:
    scale: 1
    lb_config:
      port_rules:
      - source_port: 80
        target_port: 8080
        service: letschat
    health_check:
      port: 42
      interval: 2000
      unhealthy_threshold: 3
      healthy_threshold: 2
      response_timeout: 2000
```

### Rancher Composeでのロードバランサーオプション

Rancher は HAproxy を内包したコンテナで出来た、トラフィックをサービスに振り分けるためのロードバランサーを提供します。

> **ノート:** ロードバランサーは管理ネットワーク上でのみ動作します。 もし他のネットワークを選択すると、ロードバランサーとして動作しなくなります。

ロードバランサーは他のサービスと同じようにスケジューリングされます。 詳細はRancher Composeを使ったロードバランサーの[スケジューリング]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/scheduling/#adding-labels-in-rancher-compose) を参照してください。

ロードバランシングはホストで公開されたポートとロードバランサー設定の組み合わせで構成され、これはサービスごとに固有のポート規則、カスタム構成とスティッキーポリシーを含みます。

[サイドキック]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-services/#sidekick-services)コンテナーを含むサービスを動かす場合、 [プライマリサービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-services/#primary-service) を対象に使用する必要があります、これは `サイドキック`ラベルを含むサービスです。 

### ソースポート

ロードバランサーを作成するとき、ホスト上で公開したいポートを追加できます。 これらのポートはロードバランサーのポートルールのソースポートとして使用できます。 内部でのみ使用するろーそバランサーが欲しい場合は、ロードバランサーのポートを公開せず、ロードバランサーの設定でポートルールのみを追加します。

> **ノート:** `42` 番ポートはロードバランサーのソースポートとして使用できません。 Rancher がこのポートを内部での [ヘルスチェック]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/health-checks)に使用するためです。

#### `docker-compose.yml`の例

```yaml
version: '2'
services:
  lb1:
    image: rancher/lb-service-haproxy:<version>
    # リストにあるポートはロードバランサーが動くホスト上で公開されます
    # 特定のポートにトラフィックを向かわせたい場合、ポートルールを追加する必要があります。
    ports:
    - 80
    - 81
    - 90
```

### ロードバランサーの構成

ロードバランサーの構成オプションは全て`rancher-compose.yml` の`lb_config` キー配下に定義されます。

```yaml
version: '2'
services:
  lb1:
    scale: 1
    # ロードバランサーの構成オプションはこのキーに設定してあります
    lb_config:
      port_rules:
      - source_port: 80
        target_port: 80
        service: web1
    health_check:
      port: 42
      interval: 2000
      unhealthy_threshold: 3
      healthy_threshold: 2
      response_timeout: 2000
  web1:
    scale: 2
```

#### ポートルール

ポートルールは`rancher-compose.yml`に定義されています。 ポートルールは個別に定義できるので、同じサービスに複数のポートルールができる場合があります。 デフォルトでは、Rancher はこれらのポートルールに対してある特定の優先順位に基づいて優先順位をつけます。 優先順位付けの順番を変更したい場合は、特定の[プライオリティ](#priority)を設定することもできます。

#### デフォルトの優先順位

1. ワイルドカードとURLのないホスト名
2. ワイルドカードのないホスト名
3. ワイルドカードとURLのあるホスト名
4. ワイルドカードのあるホスト名
5. URL
6. デフォルト（ホスト名なし、URLなし）

##### ソースポート

ソースポートはホスト上で公開しているポートの中の一つです（ `docker-compose.yml`にあるポートです）。

内部でのみ使用するロードバランサーを作成したい場合は、ソースポートは `docker-compose.yml` にあるポートと一致する必要はありません。

##### 対象ポート

対象ポートはサービスのプライベートポートです。このポートはサービスを開始するためのイメージで公開されているポートと紐づいています。

##### プロトコル

Rancher ロードバランサードライバーは複数のプロトコルをサポートしています。

* `http` - デフォルトでは、プロトコルを設定しない場合、ロードバランサーは`http`を使います。HAProxyはトラフィックを暗号化せず、直接通過させます。
* `tcp` - HAProxyはトラフィックを暗号化せず、直接通過させます。
* `https` - SSL終端処理が必要です。 トラフィックは指定された[証明書]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/certificates/)を使ってトラフィックを暗号化し、証明書はロードバランサーで使用される前にRancherに追加する必要があります。 ロードバランサーから対象のサービスへのトラフィックは暗号化されていません。
* `tls` - SSL終端処理が必要です。 トラフィックは指定された[証明書]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/certificates/)を使ってトラフィックを暗号化し、証明書はロードバランサーで使用される前にRancherに追加する必要があります。 ロードバランサーから対象のサービスへのトラフィックは暗号化されていません。
* `sni` - ロードバランサーとサービスへのトラフィックは暗号化されています。 ロードバランサーに複数の証明書が提供され、要求されたホスト名に応じた適切な証明書をクライアントに提示します。 (詳細は [Server Name Indication](https://en.wikipedia.org/wiki/Server_Name_Indication) を参照してください)。
* `udp` - Rancher のHAProxyではサポートしていません。

追加のロードバランサーでは一部のプロトコルしかサポートしていない可能性があります。

###### ホスト名でのルーティング

ホスト名でのルーティングは `http`、 `https` と `sni`でサポートしています。パスをベースとしたルーティングをサポートしているの `http` と `https` だけです。

##### サービス

サービス名はロードバランサーがトラフィックを向けて欲しいサービス名を指定します。 サービスが同じスタックにある場合はサービス名を使用してください。 サービスが違うスタックにある場合は、`<stack_name>/<service_name>`を使用してください。

###### `rancher-compose.yml`の例

```yaml
version: '2'
services:
  lb1:
    scale: 1
    lb_config:
      port_rules:
      - source_port: 81
        target_port: 2368
        # Service in the same stack
        service: ghost
      - source_port: 80
        target_port: 80
        # Target a service in a different stack
        service: differentstack/web1
    health_check:
      port: 42
      interval: 2000
      unhealthy_threshold: 3
      healthy_threshold: 2
      response_timeout: 2000
  ghost:
    scale: 2
```

##### ホスト名とパス

Rancher の HAProxy ロードバランサーはHOST ヘッダーとパスをポートルールに指定できるので、L7ロードバランスをサポートしています。

###### `rancher-compose.yml` の例

```yaml
version: '2'
services:
  lb1:
    scale: 1
    lb_config:
      port_rules:
      - source_port: 81
        target_port: 2368
        service: ghost
        protocol: http
        hostname: example.com
        path: /path/a
    health_check:
      port: 42
      interval: 2000
      unhealthy_threshold: 3
      healthy_threshold: 2
      response_timeout: 2000
  ghost:
    scale: 2
```

##### ワイルドカード

ホストベースルーティングを追加するとき、Rancher はワイルドカードをサポートします。以下の記法がサポートされています。

    *.domain.com -> hdr_end(host) -i .domain.com
    domain.com.* -> hdr_beg(host) -i domain.com.
    

##### 優先度

デフォルトでは、Rancherは対象となっている同一のサービスに対して[ポートルールの優先順位付け](#default-priority-order) を行いますが、希望であればポートルールの優先順をカスタムすることができます。

###### `rancher-compose.yml` の例

```yaml
version: '2'
services:
  lb1:
    scale: 1
    lb_config:
      port_rules:
      - source_port: 88
        target_port: 2368
        service: web1
        protocol: http
        hostname: foo.com
        priority: 2
      - source_port: 80
        target_port: 80
        service: web2
        protocol: http
        priority: 1
    health_check:
      port: 42
      interval: 2000
      unhealthy_threshold: 3
      healthy_threshold: 2
      response_timeout: 2000
  web1:
    scale: 2
```

##### セレクター

サービスを指定する代わりに、 [セレクター](({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/labels/#selector-labels))を設定することができます。 セレクターを使うことで、ロードバランサーではなく対象サービス上でサービスリンクトホスト名でのルーティングルールを定義できます。 セレクターに一致するラベルを持つサービスはロードバランサーの対象になります。

ロードバランサーでセレクターを使うとき、`lb_config` はロードバランサーとセレクターと一致する対象のサービスで設定できます。

ロードバランサーでは、`selector` 配下の `lb_config` で [selectorの値]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/labels/#selector-labels) を設定します。 ロードバランサーの `lb_config` のポートルールはサービスを持つ事ができず、通常は対象ポートもありません 代わりに、対象ポートは対象サービスのポートルールに設定します。 ホスト名でのルーティングを選択するのであれば、ホスト名とパスは対象サービスに設定します。

> **ノート:**v1ロードバランサーのyamlフィールドを使っているロードバランサーは、セレクターのラベルを使っているとv2ロードバランサーに変換されません。サービスのポートルールが更新されないためです。

###### `docker-compose.yml` の例

```yaml
version: '2'
services:
  lb1:
    image: rancher/lb-service-haproxy:<version>
    ports:
    - 81
  # これらのサービス (web1 と web2) ロードバランサーに対象として検出されます
  web1:
    image: nginx
      labels:
      foo: bar
  web1:
    image: nginx
    labels:
      foo: bar

```

###### `rancher-compose.yml` の例

```yaml
version: '2'
services:
  lb1:
    scale: 1
    lb_config:
      port_rules:
      - source_port: 81
        # foo=bar をラベルとして持つすべてのサービス
        selector: foo=bar
        protocol: http
    health_check:
      port: 42
      interval: 2000
      unhealthy_threshold: 3
      healthy_threshold: 2
      response_timeout: 2000
  # web1 と web2 は同じソースポートを対象にしていますが、ホスト名とパスのルールが異なります
  web1:
    scale: 1
    lb_config:
      port_rules:
      - target_port: 80
        hostname: test.com
  web2:
    scale: 1
    lb_config:
      port_rules:
      - target_port: 80
        hostname: example.com/test     
```

##### バックエンド名

ロードバランサーの設定でバックエンドに対して明示的にラベルを付けたい場合、 `バックエンド名` を使ってください。 このオプションは特定のバックエンドに対してカスタムコンフィグパラメーターを設定したい場合に便利です。

#### 証明書

`https` または `tls` [プロトコル](#protocol)を使う場合、 `rancher-compose.yml`に参照する証明書を記載してください。

##### `rancher-compose.yml`の例

```yaml
version: '2'
services:
  lb:
    scale: 1
    lb_config:
      certs:
      - <certName>
      default_cert: <defaultCertName>
```

#### カスタム構成

上級者向けに、`rancher-compose.yml`にロードバランサーのカスタム構成を指定することができます。 RancherのHAProxyに追加可能なオプションに関する詳細は[HAProxy のドキュメント](http://cbonte.github.io/haproxy-dconv/configuration-1.5.html) を参照してください。

##### `rancher-compose.yml`の例

```yaml
version: '2'
services:
  lb:
    scale: 1
    lb_config:
      config: |-
        global
            maxconn 4096
            maxpipes 1024

        defaults
            log global
            mode    tcp
            option  tcplog

        frontend 80
            balance leastconn

        frontend 90
            balance roundrobin

        backend mystack_foo
            cookie my_cookie insert indirect nocache postonly
            server $$IP <server parameters>

        backend customUUID
  health_check:
    port: 42
    interval: 2000
    unhealthy_threshold: 3
    healthy_threshold: 2
    response_timeout: 2000
```

#### スティッキーポリシー

スティッキーポリシーを指定したい場合は、 `rancher-compose.yml`のポリシーを更新することができます。

##### `rancher-compose.yml`の例

```yaml
version: '2'
services:
  lb:
    scale: 1
    lb_config:
      stickiness_policy:
        name: <policyName>
        cookie: <cookieInfo>
        domain: <domainName>
        indirect: false
        nocache: false
        postonly: false
        mode: <mode>
  health_check:
    port: 42
    interval: 2000
    unhealthy_threshold: 3
    healthy_threshold: 2
    response_timeout: 2000
```

### Rancher Compose の例

#### ロードバランサーの例 (L7)

##### `docker-compose.yml`の例

```yaml
version: '2'
services:
  web:
    image: nginx
  lb:
    image: rancher/lb-service-haproxy
  ports:
  - 80
  - 82
```

##### `rancher-compose.yml`の例

```yaml
version: '2'
services:
  lb:
    scale: 1
    lb_config:
      port_rules:
      - source_port: 80
        target_port: 8080
        service: web1
        hostname: app.example.com
        path: /foo
      - source_port: 82
        target_port: 8081
        service: web2
        hostname: app.example.com
        path: /foo/bar
  health_check:
    port: 42
    interval: 2000
    unhealthy_threshold: 3
    healthy_threshold: 2
    response_timeout: 2000
```

#### 内部向けのロードバランサーの例

内部向けのロードバランサーを設定するには、ポートはリストに出ませんが、トラフィックを向かわせるポートルールを設定することはできます。

##### `docker-compose.yml`の例

```yaml
version: '2'
services:
  lb:
    image: rancher/lb-service-haproxy
  web:
    image: nginx
```

<br />

##### `rancher-compose.yml`の例

```yaml
version: '2'
services:
  lb:
    scale: 1
    lb_config:
      port_rules:
      - source_port: 80
        target_port: 80
        service: web
    health_check:
      port: 42
      interval: 2000
      unhealthy_threshold: 3
      healthy_threshold: 2
      response_timeout: 2000
  web:
    scale: 1
```

#### SSL終端処理の例

[証明書]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/certificates/)がRancherに追加され、`rancher-compose.yml`で定義されている必要があります。

##### `docker-compose.yml`の例

```yaml
version: '2'
services:
  lb:
    image: rancher/lb-service-haproxy
    ports:
    - 443
  web:
    image: nginx
```

<br />

##### `rancher-compose.yml`の例

```yaml
version: '2'
services:
  lb:
    scale: 1
    lb_config:
      certs:
      - <certName>
      default_cert: <defaultCertName>
      port_rules:
      - source_port: 443
        target_port: 443
        service: web
        protocol: https
  web:
    scale: 1
```