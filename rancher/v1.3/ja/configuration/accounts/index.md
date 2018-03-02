* * *

title: Rancher におけるアカウント layout: rancher-default-v1.3 version: v1.3 lang: ja

* * *

## ## アカウント

### アカウントとは?

Rancher にアクセスすることができる全てのユーザーは Rancher 内でアカウントとして管理されています。 ローカル認証が設定されている場合はユーザーに対して事前にアカウントを作成することができ、他の認証プロバイダーの場合はユーザーが Rancher にログインした際にアカウントが作成されます。

#### Active Directory/GitHub/OpenLDAP 認証

[Active Directory]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/#active-directory), [Azure AD]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/#azure-ad), [GitHub]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/#github) または [OpenLDAP]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/#openldap) 認証が有効化されている場合、Rancher によって認証され、ログインしたユーザーのリストが**アカウント** タブに表示されます。 ログインによってユーザーには [サイトアクセス]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/#site-access) 権限や [環境]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/) への追加が実施されます。

#### ローカル認証

[ローカル認証]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/#local-authentication) が有効されている場合、**アカウント** タブより Rancher にアカウントを追加できます。 アカウントを Rancher 内のデータベースに追加するには **アカウントを追加** ボタンをクリックします。 アカウントを作成する際はアカウントタイプを管理者またはユーザーから選択することができます。

### アカウントタイプ

アカウントタイプはアカウントが管理者タブにアクセスできるかどうかを決定します。 Rancher における各環境では対象の環境に対して異なるアクセスレベルを提供する [メンバーシップロール]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/#membership-roles) が存在します。

#### 管理者

最初に Rancher によって認証されるユーザーが Rancher の管理者になり、管理者のみ **管理者** タブにアクセスする権限を持ちます。

環境を管理する際、管理者はたとえ管理者が対象環境のメンバーに追加されていなくても Rancher 内の全ての [環境]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/) を参照することができます。 管理者の環境のドロップダウンメニューではメンバーシップリストに記載されているメンバーのみが環境を参照することができます。

管理者は他のユーザーを Rancher の管理者に追加することができます。 追加されたユーザーは Rancher へのログイン後 **管理者** > **アカウント** ページからユーザーのロールを変更することができます。 **管理者** > **アカウント** タブで **編集** をクリック後、アカウントタイプを *管理者* に変更し **保存** をクリックします。

#### ユーザー

Rancher で認証されたユーザー以外の他のユーザーは自動的にユーザー権限を与えられ、これらのユーザーには **管理者** タブが表示されません。

また、メンバーとして所属している環境のみ参照することができます。