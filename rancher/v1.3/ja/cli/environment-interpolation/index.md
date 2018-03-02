* * *

title: Environment Interpolation in Rancher CLI における環境補間 layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## 環境補間

`rancher up` を利用する際、`rancher` コマンドを実施するマシンから `docker-compose.yml` や `rancher-compose.yml` ファイルに対し環境変数を利用することができます。 この機能は `rancher` コマンドでのみサポートされており、Rancher UI では利用できません。

### どのように使うか

`docker-compose.yml` や `rancher-compose.yml` ファイルではあなたのマシンの環境変数を参照することができます。 対象の環境変数がマシン上で定義されていない場合は変数は空文字で置き換えられます。 `Rancher` からは環境変数が定義されていない旨の warning メッセージが出力されます。 環境変数をイメージタグに利用する場合は `rancher` は最新のイメージを取得する際に `:` を取り除かない点に注意してください。 例としてイメージ名 `<imagename>:` は無効なイメージ名となりコンテナはデプロイされません。 また、全ての環境変数がマシン上で定義されており、正しい値であることを確認してください。

#### 例

`rancher` コマンドを実施するマシン上で環境変数 `IMAGE_TAG=14.04` が定義されているとします。

```bash
# 環境変数としてイメージタグが設定されている
$ env | grep IMAGE
IMAGE_TAG=14.04
# スタック を起動
$ rancher up
```

**例 `docker-compose.yml`**

```yaml
version: '2'
services:
  ubuntu:
    tty: true
    image: ubuntu:$IMAGE_TAG
    stdin_open: true
```

<br />

Rancher 上では `ubuntu` サービスが `ubuntu:14.04` イメージからデプロイされます。

### 環境補間フォーマット

`Rancher` では `docker-compose` と同様のフォーマットをサポートしています。

```yaml
version: '2'
services:
  web:
    # 括弧で閉じていない名前
    image: "$IMAGE"

    # 括弧で閉じられた名前
    command: "${COMMAND}"

    # 配列要素
    ports:
    - "${HOST_PORT}:8000"

    # 連想配列名
    labels:
      mylabel: "${LABEL_VALUE}"

    # 未定義の値 - 以下の場合 "host-" として展開されます
    hostname: "host-${UNSET_VALUE}"

    # エスケープ補間 - 以下の場合 "${ESCAPED}" として展開されます
    command: "$${ESCAPED}"
```