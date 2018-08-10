---
title: Azure コンテナー レジストリでの認証
description: Azure Active Directory サービス プリンシパルによるレジストリへの直接ログインなど、Azure コンテナー レジストリの認証オプションについて説明します。
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2018
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30ca8fe89105584b1062c5a068e107bdfde154fc
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579522"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>プライベート Docker コンテナー レジストリによる認証

Azure コンテナー レジストリでの認証には複数の方法があり、それぞれ 1 つ以上のレジストリ使用シナリオに適用できます。

[個々のログイン](#individual-login-with-azure-ad)によって直接レジストリにログインすることも、Azure Active Directory (Azure AD) [サービス プリンシパル](#service-principal)を使用して、アプリケーションやコンテナー オーケストレーターが無人 (「ヘッドレス」) 認証を実行することもできます。

Azure Container Registry では、認証されていない Docker 操作と匿名アクセスはサポートされていません。 パブリック イメージでは、[Docker Hub](https://docs.docker.com/docker-hub/) をご利用いただけます。

## <a name="individual-login-with-azure-ad"></a>Azure AD での個々のログイン

開発ワークステーションとの間でのイメージのプッシュやプルなど、レジストリを直接操作するときは、[Azure CLI](/cli/azure/install-azure-cli) で [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) コマンドを使用して認証します。

```azurecli
az acr login --name <acrName>
```

`az acr login` を使用してログインすると、CLI は `az login` の実行時に作成されたトークンを使用して、レジストリとのセッションをシームレスに認証します。 この方法でログインすると、資格情報がキャッシュされるので、以降の `docker` コマンドではユーザー名やパスワードが不要になります。 トークンの有効期限が切れた場合は、`az acr login` コマンドを再度使用して再認証することで、トークンを更新できます。 Azure ID で `az acr login` を使用すると、[ロールベースのアクセス](../role-based-access-control/role-assignments-portal.md)が可能になります。

## <a name="service-principal"></a>サービス プリンシパル

アプリケーションやサービスがヘッドレス認証に使用できる[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)をレジストリに割り当てることができます。 サービス プリンシパルを使用すると、レジストリへの[ロールベースのアクセス](../role-based-access-control/role-assignments-portal.md)が可能になります。1 つのレジストリに複数のサービス プリンシパルを割り当てることができます。 複数のサービス プリンシパルを割り当てることで、アプリケーションごとに異なるアクセスを定義できます。

使用可能なロールは次のとおりです。

  * **閲覧者**: プル
  * **共同作成者**: プルとプッシュ
  * **所有者**: プル、プッシュ、他のユーザーへのロールの割り当て

サービス プリンシパルを使用すると、次のようなプッシュ シナリオとプル シナリオでレジストリへのヘッドレス接続が可能になります。

  * "*閲覧者*": レジストリからオーケストレーション システム (Kubernetes、DC/OS、Docker Swarm など) へのコンテナーのデプロイ。 また、コンテナー レジストリから、[AKS](../aks/index.yml)、[App Service](../app-service/index.yml)、[Batch](../batch/index.yml)、[Service Fabric](/azure/service-fabric/) などの関連する Azure サービスにプルすることもできます。

  * "*共同作成者*": コンテナー イメージを作成してレジストリにプッシュする、継続的インテグレーションおよびデプロイ ソリューション (Visual Studio Team Services (VSTS) や Jenkins など)。

> [!TIP]
> [az ad sp reset-credentials](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-reset-credentials) コマンドを実行することで、サービス プリンシパルのパスワードを再生成できます。
>

サービス プリンシパルを使用して直接ログインすることもできます。 サービス プリンシパルのアプリ ID とパスワードを `docker login` コマンドに指定します。

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

いったんログインすると Docker によって資格情報がキャッシュに格納されるため、アプリ ID を覚えておく必要はありません。

インストールされている Docker のバージョンによっては、`--password-stdin` パラメーターの使用を勧めるセキュリティの警告が表示される場合があります。 このパラメーターの使用について、ここでは説明していませんが、このベスト プラクティスに従うことをお勧めします。 詳細については、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドのリファレンスを参照してください。

サービス プリンシパルを用いた ACR へのヘッドレス認証の詳細については、「[Azure Container Registry authentication with service principals (サービス プリンシパルによる Azure Container Registry 認証)](container-registry-auth-service-principal.md)」をご覧ください。

## <a name="admin-account"></a>管理者アカウント

各コンテナー レジストリには管理者ユーザー アカウントが含まれており、このアカウントは既定で無効になっています。 [Azure Portal](container-registry-get-started-portal.md#create-a-container-registry) または Azure CLI を使用して、管理者ユーザーを有効にし、その資格情報を管理できます。

> [!IMPORTANT]
> 管理者アカウントは、主にテストのために、1 人のユーザーがレジストリにアクセスすることを目的としています。 管理者アカウントの資格情報を複数のユーザーと共有しないようにすることをお勧めします。 管理者アカウントで認証するすべてのユーザーが、レジストリへのプル/プッシュアクセス権を持つ 1 人のユーザーとして表示されます。 このアカウントを変更したり、無効にしたりすると、その資格情報を使用するすべてのユーザーのレジストリ アクセスが無効になります。 ユーザーおよびヘッドレス シナリオ用のサービス プリンシパルには、個人 ID を使用することをお勧めします。
>

管理者アカウントには、再生成できる 2 つのパスワードを指定します。 2 つのパスワードにより、一方のパスワードを再生成している間に、もう一方のパスワードを使用してレジストリへの接続を維持できます。 管理者アカウントが有効になっている場合、ユーザー名とどちらかのパスワードを `docker login` コマンドに渡して、レジストリに対する基本認証を実行できます。 例: 

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

この場合も、セキュリティを強化するために、コマンドラインでパスワードを指定するのではなく、`--password-stdin` パラメーターを使用するよう Docker から勧められます。 `-p` を指定せずにユーザー名だけを指定し、入力を要求された時点でパスワードを入力することもできます。

既存のレジストリの管理者ユーザーを有効にするには、Azure CLI で [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) コマンドの `--admin-enabled` パラメーターを使用します。

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Azure Portal で管理者ユーザーを有効にするには、レジストリに移動し、**[設定]** の **[アクセス キー]** を選択して、**[管理者ユーザー]** の **[有効にする]** を選択します。

![Azure Portal の管理者ユーザーを有効にする UI][auth-portal-01]

## <a name="next-steps"></a>次の手順

* [Azure CLI を使用した最初のイメージのプッシュ](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
