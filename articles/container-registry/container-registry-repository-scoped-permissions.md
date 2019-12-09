---
title: リポジトリに対するアクセス許可
description: レジストリ内の特定のリポジトリをスコープとしたアクセス許可を持つトークンを作成して、イメージをプルまたはプッシュする
ms.topic: article
ms.date: 10/31/2019
ms.openlocfilehash: cf36a49ffd6c04897e6f44b844f0c813d0992b18
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454915"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Azure Container Registry でのリポジトリをスコープとしたアクセス許可 

Azure Container Registry では、レジストリ全体への[ロールベースのアクセス](container-registry-roles.md)を持つ ID を使用して、いくつかの[認証オプション](container-registry-authentication.md)がサポートされています。 ただし、特定のシナリオでは、レジストリ内の特定の*リポジトリ*のみへのアクセス権を付与することが必要になる場合があります。 

この記事では、レジストリ内の特定のリポジトリに対してのみアクションを実行するアクセス許可を持つアクセス トークンを作成し使用する方法について説明します。 アクセス トークンを使用すると、イメージをプルまたはプッシュしたり、その他の操作を実行したりするための、リポジトリをスコープとする期間限定のアクセス権をユーザーまたはサービスに付与することができます。 

トークンの概念とシナリオの背景については、この記事の後半の「[リポジトリをスコープとしたアクセス許可について](#about-repository-scoped-permissions)」を参照してください。

> [!IMPORTANT]
> この機能は現在プレビュー段階であり、一定の[制限事項が適用されます](#preview-limitations)。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="preview-limitations"></a>プレビューの制限事項

* この機能は、**Premium** コンテナー レジストリでのみ使用できます。 レジストリ サービスのレベルと制限については、「[Azure Container Registry SKU](container-registry-skus.md)」をご覧ください。
* 現在、リポジトリをスコープとしたアクセス許可を、サービス プリンシパルやマネージド ID などの Azure Active Directory オブジェクトに割り当てることはできません。

## <a name="prerequisites"></a>前提条件

* **Azure CLI** - この記事では、Azure CLI (バージョン 2.0.76 以降) のローカル インストールが必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。
* **Docker** - レジストリに対する認証を行うには、ローカルの Docker インストールも必要です。 Docker では、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、および [Linux](https://docs.docker.com/engine/installation/#supported-platforms) システム向けのインストールの指示を提供しています。
* **リポジトリを含むコンテナー レジストリ** - コンテナー レジストリを用意していない場合は、ご利用の Azure サブスクリプションで作成します。 たとえば、[Azure Portal](container-registry-get-started-portal.md) または [Azure CLI](container-registry-get-started-azure-cli.md) を使用します。 

  テスト目的の場合は、1 つまたは複数のサンプル イメージをレジストリに[プッシュ](container-registry-get-started-docker-cli.md)または[インポート](container-registry-import-images.md)します。 この記事の例では、2 つのリポジトリにある次のイメージが参照されています: `samples/hello-world:v1` および `samples/nginx:v1`。 

## <a name="create-an-access-token"></a>アクセス トークンを作成する

[az acr token create][az-acr-token-create] コマンドを使用してトークンを作成します。 トークンを作成する場合は、1 つまたは複数のリポジトリを指定してから各リポジトリに関連付けられたアクションを指定するか、それらの設定で既存のスコープ マップを指定します。

### <a name="create-access-token-and-specify-repositories"></a>アクセス トークンを作成し、リポジトリを指定する

次の例では、`samples/hello-world` リポジトリに対して `content/write` および `content/read` アクションを実行するアクセス許可、`samples/nginx` リポジトリに対して `content/read` アクションを実行するアクセス許可を持つアクセス トークンを作成します。 既定では、このコマンドによって 2 つのパスワードが生成されます。 

この例では、トークンの状態が `enabled` に設定されています (既定の設定) が、トークンはいつでも更新が可能であり、状態を `disabled` に設定することもできます。

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

出力には、生成されたパスワードやスコープ マップなど、トークンに関する詳細が表示されます。 パスワードは後で `docker login` で使用できるように安全な場所に保存することをお勧めします。 そのパスワードを再度取得することはできませんが、新しいパスワードを生成することはできます。

また、出力には、スコープ マップが自動的に作成され、`MyToken-scope-map` という名前が付けられたことも示されます。 スコープ マップを使用することで、同じリポジトリ アクションを他のトークンにも適用できます。 後でスコープ マップを更新して、トークンのアクセス許可を変更することもできます。

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

### <a name="create-a-scope-map-and-associated-token"></a>スコープ マップおよび関連するトークンを作成する

あるいは、トークンを作成するときに、リポジトリおよび関連付けられたアクションを含むスコープ マップを指定します。 スコープ マップを作成するには、[az acr scope-map create][az-acr-scope-map-create] コマンドを使用します。

次のコマンド例では、前の例で使用したのと同じアクセス許可を持つスコープ マップが作成されます。 これにより、`samples/hello-world` リポジトリに対する `content/write` アクションと `content/read` アクション、`content/read` リポジトリに対する `samples/nginx` アクションが可能になります。

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

次のような出力になります。

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

[az acr token create][az-acr-token-create] を実行して、*MyScopeMap* スコープ マップに関連付けられたトークンを作成します。 既定では、このコマンドによって 2 つのパスワードが生成されます。 この例では、トークンの状態が `enabled` に設定されています (既定の設定) が、トークンはいつでも更新が可能であり、状態を `disabled` に設定することもできます。

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

出力には、生成されたパスワードや適用したスコープ マップなど、トークンに関する詳細が表示されます。 パスワードは後で `docker login` で使用できるように安全な場所に保存することをお勧めします。 そのパスワードを再度取得することはできませんが、新しいパスワードを生成することはできます。

## <a name="generate-passwords-for-token"></a>トークンのパスワードを生成する

トークンの作成時にパスワードが作成された場合は、[レジストリに対する認証](#authenticate-using-token)に関するセクションに進みます。

トークン パスワードがない場合、または新しいパスワードを生成する場合は、[az acr token credential generate][az-acr-token-credential-generate] コマンドを実行します。

次の例では、作成したトークンの新しいパスワードが 30 日の有効期間で生成されます。 このパスワードは、環境変数 TOKEN_PWD に格納されます。 この例は Bash シェル用にフォーマットされています。

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>トークンを使用して認証を行う

トークン資格情報を使用してレジストリに対する認証を行うには、`docker login` を実行します。 ユーザー名としてトークン名を入力してから、そのパスワードのいずれかを指定します。 次の例については、Bash シェル用にフォーマットされており、環境変数を使用して値が指定されています。

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

出力には、認証に成功したことが表示されます。

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>スコープ指定されたアクセスを確認する

レジストリ内のリポジトリをスコープとするアクセス許可がトークンによって付与されていることを確認できます。 この例では、次の `docker pull` コマンドが正常に完了して、`samples/hello-world` リポジトリと `samples/nginx` リポジトリ内の使用可能なイメージがプルされています。

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

この例のトークンでは、`samples/hello-world` リポジトリでのみ `content/write` アクションが許可されているため、そのリポジトリに対して `docker push` は成功しますが、`samples/nginx` に対しては失敗します。

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>スコープ マップとトークンを更新する

トークンのアクセス許可を更新するには、[az acr scope-map update][az-acr-scope-map-update] を使用して、関連付けられているスコープ マップ内のアクセス許可を更新します。 たとえば、*MyScopeMap* を更新して、`samples/hello-world` リポジトリに対する `content/write` アクションを削除するには、次のようにします。

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

スコープ マップが複数のトークンに関連付けられている場合、このコマンドでは、関連付けられているすべてのトークンのアクセス許可が更新されます。

別のスコープ マップを使用してトークンを更新する場合は、[az acr token update][az-acr-token-update] を実行します。 例:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

トークンを更新した後、またはトークンに関連付けられたスコープ マップを更新した後、アクセス許可の変更は、次の `docker login` またはトークンを使用したその他の認証で有効になります。

トークンを更新したら、新しいパスワードを作成してレジストリにアクセスすることをお勧めします。 [az acr token credential generate][az-acr-token-credential-generate] を実行します。 例:

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>リポジトリをスコープとしたアクセス許可について

### <a name="concepts"></a>概念

リポジトリをスコープとしたアクセス許可を構成するには、Azure CLI 内のコマンドを使用して、*アクセス トークン*および関連付けられた "*スコープ マップ*" を作成します。

* **アクセス トークン** とは、レジストリに対する認証を行う場合にパスワードと一緒に使用される資格情報です。 各トークンに関連付けられている "*アクション*" は、1 つまたは複数のリポジトリを対象として許可されます。 各トークンの有効期限を設定できます。 

* 指定された各リポジトリに対する**アクション**には、次のうちの 1 つまたは複数のものが含まれます。

  |Action  |説明  |
  |---------|---------|
  |`content/read`     |  リポジトリからデータを読み取ります。 たとえば、アーティファクトをプルします。  |
  |`metadata/read`    | リポジトリからメタデータを読み取ります。 たとえば、タグを一覧したり、マニフェスト メタデータを表示したりします。   |
  |`content/write`     |  リポジトリにデータを書き込みます。 `content/read` と共に使用して、アーティファクトをプッシュします。    |
  |`metadata/write`     |  メタデータをリポジトリに書き込みます。 たとえば、マニフェストの属性を更新します。  |
  |`content/delete`    | リポジトリからデータを削除します。 たとえば、リポジトリまたはマニフェストを削除します。 |

* **スコープ マップ** とはレジストリ オブジェクトであり、トークンに適用する、または他のトークンに再適用できるリポジトリ アクセス許可が、これによってグループ化されます。 トークンの作成時にスコープ マップを適用しなかった場合は、アクセス許可の設定を保存するために、スコープ マップがお客様に代り自動的に作成されます。 

  スコープ マップは、一連のリポジトリに対して同じアクセス権を持つ複数のユーザーを構成するのに役立ちます。 Azure Container Registry には、アクセス トークンの作成時に適用できるシステム定義のスコープ マップも用意されています。

次の図は、トークンとスコープ マップの関係をまとめたものです。 

![レジストリのスコープ マップとトークン](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>シナリオ

アクセス トークンを使用するシナリオは次のとおりです。

* リポジトリからイメージをプルできるように IoT デバイスに個別のトークンを提供する
* 特定のリポジトリへのアクセス許可を外部組織に付与する 
* ご自分の組織内の特定のユーザー グループへのリポジトリ アクセスを制限する。 たとえば、特定のリポジトリをターゲットとしたイメージを構築する開発者に書き込みおよび読み取りアクセス権を付与し、それらのリポジトリからデプロイするチームに読み取りアクセス権を付与します。

### <a name="authentication-using-token"></a>トークンを使用した認証

ユーザー名としてトークン名を使用すると共に、それに関連付けられているパスワードの 1 つを使用して、ターゲット レジストリに対する認証を行います。 認証方法は、構成されているアクションによって異なります。

### <a name="contentread-or-contentwrite"></a>content/read または content/write

トークンによって許可されているのが `content/read` アクションまたは `content/write` アクションのみである場合は、次の認証フローのいずれかでトークン資格情報を指定します。

* `docker login` を使用して Docker に対する認証を行う
* Azure CLI の [az acr login][az-acr-login] コマンドを使用してレジストリに対する認証を行う

認証後、スコープとした 1 つまたは複数のリポジトリに対して構成されたアクションがトークンによって許可されます。 たとえば、リポジトリに対する `content/read` アクションがトークンによって許可されている場合、そのリポジトリ内のイメージに対して `docker pull` 操作が許可されます。

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>metadata/read、metadata/write、または content/delete

リポジトリに対する `metadata/read`、`metadata/write`、または `content/delete` アクションがトークンによって許可されている場合、Azure CLI の [az acr repository][az-acr-repository] コマンドを使用して、トークン資格情報をパラメーターとして指定する必要があります。

たとえば、リポジトリに対して `metadata/read` アクションが許可されている場合は、タグを一覧する [az acr repository show-tags][az-acr-repository-show-tags] コマンドを実行するときに、トークン資格情報を渡します。

## <a name="next-steps"></a>次の手順

* スコープ マップとアクセス トークンを管理するには、[az acr scope-map][az-acr-scope-map] および [az acr token][az-acr-token] コマンド グループ内で追加のコマンドを使用します。
* 管理者アカウントまたは Azure Active Directory ID を使用して Azure コンテナー レジストリに対する認証を行うシナリオについては、[認証の概要](container-registry-authentication.md)に関するページを参照してください。


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
