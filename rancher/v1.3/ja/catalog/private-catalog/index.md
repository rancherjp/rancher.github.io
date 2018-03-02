* * *

title: プライベートカタログの作成 layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## プライベートカタログの作成

Rancher カタログサービスでは、Rancher が解釈できるようにプライベートカタログが 特定のフォーマットで構成されている必要があります。

### テンプレートフォルダー

カタログテンプレートは、環境に対して選択したコンテナーオーケストレーションタイプに基づいて、 Rancher 内で表示されます。

#### 各オーケストレーションタイプのテンプレート

* *Cattle* : `templates` フォルダーの UI に入っています。
* *[Kubernetes]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/kubernetes/)* : `kubernetes-templates` フォルダーの UI に入っています。
* *[Swarm]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/swarm/)* : `swarm-templates` フォルダーのUIに入っています。
* *[Mesos]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/mesos/)* : `mesos-templates` フォルダーのUI に入っています。

### インフラストラクチャサービステンプレート

[環境テンプレート]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/#what-is-an-environment-template)において利用できる[インフラストラクチャサービス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/rancher-services/)は、Rancherで有効になっているカタログの `infra-templates` フォルダーの中にあります。

これらのサービスは**カタログ**タブから利用でき、選択したオーケストレーションタイプでは動作しないようなインフラストラクチャサービスも含めて全て表示されます。 インフラストラクチャサービスはカタログから直接起動するのではなく、環境テンプレートの作成時に選択することを推奨します。

### ディレクトリ構造

    -- templates (Or any of templates folder)
      |-- cloudflare
      |   |-- 0
      |   |   |-- docker-compose.yml
      |   |   |-- rancher-compose.yml
      |   |-- 1
      |   |   |-- docker-compose.yml
      |   |   |-- rancher-compose.yml
      |   |-- catalogIcon-cloudflare.svg
      |   |-- config.yml
    ...
    

<br />

メインのディレクトリには `templates` フォルダーが必要です。 `templates` フォルダーには作成した各カタログエントリーのフォルダーが含まれます。 各カタログエントリーにはフォルダー名として簡単なテンプレート名をつけることを推奨します。

カタログエントリーフォルダー（例： `cloudflare` ）では、作成した各バージョンのフォルダーがあります。 最初のバージョンは `` にし、その後のバージョンはそこからの増分値にして下さい。 例にあるように、2番目のバージョンは `1` フォルダーです。 新しい番号のフォルダーを用意すれば、以前のバージョンのテンプレートからスタックを更新する方法を取ることができます。 または、テンプレートの `` フォルダーを更新してデプロイしなおす方法もあります。

> **ノート:** 各カタログのエントリーは単一の単語である必要があるので、長井カタログ名の場合はスペースの代わりに `-` を使って下さい。 スペースは `config.yml` の `name` セクションで使用できます。

### Rancher カタログに表示する Rancher カタログファイル

カタログエントリーフォルダーの中で、作成したカタログエントリーの詳細を表示するには、2つのファイルを使います。

* まず最初の `config.yml` にはエントリーの詳細な情報が入ります。

```yaml
name: # カタログエントリーの名前
description: |
  # カタログエントリーの説明
version: # 使用するカタログのバージョン
category: # カタログエントリーの検索に使用するカテゴリー
maintainer: # カタログエントリーのメンテナー
license: # ライセンス形態
projectURL: # カタログエントリーに関する URL
```

<br />

* 2番目のファイルはカタログエントリーのアイコンイメージです。ファイルは先頭に `catalogIcon-` をつける必要があります。

カタログエントリー毎に、少なくとも`config.yml`, `catalogIcon-entry.svg`, 最初のバージョンである `` フォルダーの3つのアイテムがあります。

### Rancher カタログテンプレート

Rancher で [Rancher Compose]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/cattle/adding-services/#adding-services-with-rancher-compose) を使ってサービスをデプロイするためには、`docker-compose.yml` と `rancher-compose.yml` の2つのファイルがが必要です。 これらのファイルはバージョン番号（例：0,1,etc）のフォルダ内にあります。

`docker-compose.yml` は、 `docker-compose up` で起動できるファイルでなければいけません。サービスは docker-compose のフォーマットに従います。

`rancher-compose.yml` にはカタログエントリーをカスタムするときに役立つ追加の情報が含まれます。 `.catalog` セクションには、カタログエントリーが正しく解釈されるために必要ないくつかのフィールドがあります。

追加で `README.md` を作成することができ、長い説明やカタログサービスの使い方を記述できます。

**`rancher-compose.yml`**

```yaml
version: '2'
catalog:
  name: # カタログテンプレートの名前
  version: # カタログテンプレートのバージョン番号
  description: # カタログテンプレートの説明
  minimum_rancher_version: # テンプレートをサポートする Rancher の最小バージョン。 v1.0.1 や 1.0.1 が記述可能
  maximum_rancher_version: # テンプレートをサポートする Rancher の最大バージョン。 v1.0.1 や 1.0.1 が記述可能
  upgrade_from: # このテンプレートにアップグレードできる以前のバージョン
  questions: # 構成オプションをユーザーに入力させるために使います
```

<br />

upgrade_from には、3タイプの値を使うことができます。

1. 1つのバージョンからのみアップグレードが可能な場合: `1.0.0`
2. 特定のバージョンより高いか低いかを選択できる場合: `>=1.0.0.`, `<=2.0.0`
3. バージョンをレンジで指定する場合: `>1.0.0 <2.0.0 || >3.0.0`

### `rancher-compose.yml` の Questions について

`.catalog` の `questions` セクションを使うことで、利用者はサービスの設定オプションを変更できます。 入力した答え （`answers`）は、サービスが起動する前に `docker-compose.yml` に投入されます。

設定オプションは、 `rancher-compose.yml` の `questions` セクションにある項目で定義します。

```yaml
version: '2'
.catalog:
  questions:
    - variable: # 質問と答えを紐づけるための単語
      label: # 答えてもらうための質問項目
      description: | # 質問への答え方を含めた詳細な説明
      default: # （任意） UI にあらかじめ表示させておくデフォルトの値
      required: # (任意) 回答が必須かどうか。 デフォルトでは `false` です。
      type: # 質問項目のフォーマットと、入力してほしいデータの型
```

<br />

#### Type

`type` セクションでは、UI での質問形式と入力してほしいデータの型を制御します。

望ましいフォーマットは以下：

* `string` 回答を入力するためのテキストボックスが表示され、答えは文字列として処理されます。
* `int` 回答を入力するためのテキストボックスが表示され、答えは数値として処理されます。 この UI はテンプレートが起動する前に有効な数値かどうか検証します。
* `boolean` 回答を入力するためのラジオボタンが表示され、回答は`true` または `false` として処理されます。 ラジオボタンにチェックが入ると、回答は `true` として処理されます。
* `password` 回答を入力するためのテキストボックスが表示され、答えは文字列として処理されます。
* `service` 環境にある全てのサービスがドロップダウン形式で表示されます。
* `enum` ドロップダウンが UI に表示され、ドロップダウンの表示項目は`options` セクションの内容です。

```yaml
version: '2'
catalog:
  questions:
    - variable:
      label:
      description: |
      type: enum   
      options: #  `enum`を使った場合のオプション項目
        - Option 1
        - Option 2
```

* `multiline` UI に複数行のテキストボックスが表示されます

```yaml
version: '2'
catalog:
  questions:
    - variable:
      label:
      description: |
      type: multiline
      default: |
        それぞれの行が
        別々に
        分かれて
        表示されます
```

* `certificate` その環境で使用できる証明書がドロップダウンで表示されます。

```yaml
version: '2'
catalog:
  questions:
    - variable:
      label:
      description: |
      type: certificate
```

### Yeoman によるカタログジェネレーター

[Yeoman](http://yeoman.io/) 元に作成されている [open-source project](https://github.com/slashgear/generator-rancher-catalog) があり、それを使うことでカタログエントリーのテンプレートのスケルトンを作成できます。