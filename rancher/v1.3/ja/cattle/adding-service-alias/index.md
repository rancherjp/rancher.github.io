* * *

title: サービスのエイリアス layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## サービスのエイリアス

サービスのエイリアスを設定することで、サービス名を直接指定する代わりに、サービスのエイリアスを使って特定のサービスを指定することができるようになります。

### UIでサービスのエイリアスを設定する

**サービスを追加**ボタンの隣のドロップダウンアイコンをクリックすることでスタック内部のサービスに対してエイリアスを設定することができます。 **サービスのエイリアスを追加**を選択することでサービスのエイリアスを指定できます。 他の方法として、スタックの画面を見ている場合、同じ**サービスを追加**ドロップダウンで、同様の設定を行うことができます。

エイリアスを設定する際は、**名前**を設定しなければなりません。加えて、必要であればサービスの**詳細情報**を追加します。**名前**は選択したサービスのエイリアスとなります。

エイリアスを追加したい対象を選択します。スタック内にすでに作成済みのあらゆるサービスを対象として選択できます。最後に**作成**をクリックします。

エイリアスを設定済みのサービスのリストはサービスの画面で確認することができます。

#### サービスの追加と削除

サービスエイリアスに含まれるサービスはいつでも編集できます。 サービスのドロップダウンメニュー内部の**編集**をクリックします。 エイリアスに追加のサービスを含めることや、既存のサービスを取り除くといったことが可能です。

### Rancher Composeでサービスのエイリアスを追加する

A service alias creates a pointer to service(s). In the example below, `web[.stack-name.rancher.internal]` will resolve to the IPs of the containers of `web1` and `web2`. The `rancher/dns-service` is not an actual image, but is required for the `docker-compose.yml`. There are no containers created for alias services.

#### Example `docker-compose.yml`

```yaml
version: '2'
services:
  web:
    image: rancher/dns-service
    links:
    - web1
    - web2

  web1:
    image: nginx

  web2:
    image: nginx
```