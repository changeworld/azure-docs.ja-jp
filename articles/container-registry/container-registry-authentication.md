---
title: レジストリ認証オプション
description: Azure Active Directory ID でのサインイン、サービス プリンシパルの使用、オプションの管理者資格情報の使用など、非公開の Azure コンテナー レジストリのための認証オプション。
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 384f401a986c58dc6ce63384ce3e2a43b8db27fa
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029879"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Azure コンテナー レジストリでの認証

Azure コンテナー レジストリでの認証には複数の方法があり、それぞれ 1 つ以上のレジストリ使用シナリオに適用できます。

[個々のログイン](#individual-login-with-azure-ad)によって直接レジストリに対して認証を受けるなどの方法が推奨されます。またはアプリケーションやコンテナー オーケストレーターで Azure Active Directory (Azure AD) [サービス プリンシパル](#service-principal)を使用して無人 ("ヘッドレス") 認証を実行することができます。

## <a name="authentication-options"></a>認証オプション

次の表に、使用可能な認証方法と推奨されているシナリオを示します。 詳細については、リンク先のコンテンツを参照してください。

| Method                               | 認証方法                                           | シナリオ                                                            | RBAC                             | 制限事項                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [個人の AD ID](#individual-login-with-azure-ad)                |  Azure CLI  の `az acr login`                            | 開発者、テスト担当者による対話型のプッシュ/プル                                    | はい                              | AD トークンを 3 時間ごとに更新する必要がある     |
| [AD サービス プリンシパル](#service-principal)                  | `docker login`<br/><br/>Azure CLI の `az acr login`<br/><br/> API またはツールのレジストリ ログイン設定<br/><br/> Kubernetes のプル シークレット                                           | CI/CD パイプラインからの無人プッシュ<br/><br/> Azure または外部サービスへの無人プル  | はい                              | SP パスワードの既定の有効期限は 1 年である       |                                                           
| [AKS との統合](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | AKS クラスターが作成または更新されたときにレジストリをアタッチする  | AKS クラスターへの無人プル                                                  | いいえ、プル アクセスのみ             | AKS クラスターでしか使用できない            |
| [Azure リソースのマネージド ID](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/>Azure CLI の  `az acr login`                                        | Azure CI/CD パイプラインからの無人プッシュ<br/><br/> Azure サービスへの無人プル<br/><br/>   | はい                              | [Azure リソースのマネージド ID をサポートする](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) Azure サービスからのみ使用              |
| [管理者ユーザー](#admin-account)                            | `docker login`                                          | 個人の開発者またはテスト担当者による対話型のプッシュ/プル                           | いいえ、常にプルおよびプッシュ アクセス  | レジストリごとに 1 つのアカウント (複数のユーザーの場合は推奨されません)         |
| [リポジトリをスコープとしたアクセス トークン](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>Azure CLI の `az acr login`   | 個人の開発者またはテスト担当者によるリポジトリへの対話型のプッシュ/プル<br/><br/> 個々のシステムまたは外部デバイスによるリポジトリへの無人プッシュ/プル                  | はい                              | 現時点では AD ID と統合されていない  |

## <a name="individual-login-with-azure-ad"></a>Azure AD での個々のログイン

開発ワークステーションとの間でのイメージのプッシュやプルなど、レジストリを直接操作するときは、[Azure CLI](/cli/azure/install-azure-cli) で [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) コマンドを使用して認証します。

```azurecli
az acr login --name <acrName>
```

`az acr login` を使用してログインすると、CLI では [az login](/cli/azure/reference-index#az-login) の実行時に作成されたトークンを使用して、レジストリとのセッションがシームレスに認証されます。 認証フローを完了するには、お使いの環境に Docker をインストールして実行する必要があります。 `az acr login` では、Docker クライアントを使用して、`docker.config` ファイル内に Azure Active Directory トークンが設定されます。 この方法でログインすると、資格情報がキャッシュされるので、セッションの以降の `docker` コマンドではユーザー名やパスワードが不要になります。

> [!TIP]
> また、[OCI 成果物](container-registry-oci-artifacts.md)など、Docker イメージ以外の成果物をレジストリに対してプッシュまたはプルする場合は、`az acr login` を使用して個々の ID を認証します。  


レジストリのアクセスでは、`az acr login` で使用されるトークンは **3 時間**有効なため、`docker` コマンドを実行する前に常にレジストリにログインすることをお勧めします。 トークンの有効期限が切れた場合は、`az acr login` コマンドを再度使用して再認証することで、トークンを更新できます。 

Azure ID で `az acr login` を使用すると、[ロールベースのアクセス](../role-based-access-control/role-assignments-portal.md)が可能になります。 Azure AD の自分個人の ID でレジストリにログインすることが推奨されるシナリオもあります。 サービス間のシナリオの場合や、個々のアクセスの管理は行わないワークグループまたは開発ワークフローのニーズを処理する場合は、[Azure リソース用のマネージド ID](container-registry-authentication-managed-identity.md) でログインすることもできます。

## <a name="service-principal"></a>サービス プリンシパル

[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md)をレジストリに割り当てた場合、アプリケーションやサービスではヘッドレス認証にそれを使用できます。 サービス プリンシパルを使用すると、レジストリへの[ロールベースのアクセス](../role-based-access-control/role-assignments-portal.md)が可能になります。1 つのレジストリに複数のサービス プリンシパルを割り当てることができます。 複数のサービス プリンシパルを割り当てることで、アプリケーションごとに異なるアクセスを定義できます。

コンテナー レジストリでは次のロールを使用できます。

* **AcrPull**: プル

* **AcrPush**: プルとプッシュ

* **所有者**: プル、プッシュ、他のユーザーへのロールの割り当て

ロールの完全な一覧については、「[Azure Container Registry のロールとアクセス許可](container-registry-roles.md)」をご覧ください。

Azure コンテナー レジストリを使用した認証のためのサービス プリンシパルを作成する CLI スクリプトと詳しいガイダンスについては、「[サービス プリンシパルによる Azure Container Registry 認証](container-registry-auth-service-principal.md)」を参照してください。

## <a name="admin-account"></a>管理者アカウント

各コンテナー レジストリには管理者ユーザー アカウントが含まれており、このアカウントは既定で無効になっています。 Azure portal で、または Azure CLI や他の Azure ツールを使用して、管理者ユーザーを有効にし、その資格情報を管理できます。

> [!IMPORTANT]
> 管理者アカウントは、主にテストのために、1 人のユーザーがレジストリにアクセスすることを目的としています。 管理者アカウントの資格情報を複数のユーザーで共有しないことをお勧めします。 管理者アカウントで認証するすべてのユーザーが、レジストリへのプル/プッシュアクセス権を持つ 1 人のユーザーとして表示されます。 このアカウントを変更したり、無効にしたりすると、その資格情報を使用するすべてのユーザーのレジストリ アクセスが無効になります。 ユーザーおよびヘッドレス シナリオ用のサービス プリンシパルには、個人 ID を使用することをお勧めします。
>

管理者アカウントには、再生成できる 2 つのパスワードを指定します。 2 つのパスワードにより、一方のパスワードを再生成している間に、もう一方のパスワードを使用してレジストリへの接続を維持できます。 管理者アカウントが有効になっている場合は、レジストリに対する基本認証についてのメッセージが表示されたら、ユーザー名とどちらかのパスワードを `docker login` コマンドに渡すことができます。 次に例を示します。

```
docker login myregistry.azurecr.io 
```

ログイン資格情報の管理のベスト プラクティスについては、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドのリファレンスを参照してください。

既存のレジストリの管理者ユーザーを有効にするには、Azure CLI で [az acr update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) コマンドの `--admin-enabled` パラメーターを使用します。

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Azure Portal で管理者ユーザーを有効にするには、レジストリに移動し、 **[設定]** の **[アクセス キー]** を選択して、 **[管理者ユーザー]** の **[有効にする]** を選択します。

![Azure Portal の管理者ユーザーを有効にする UI][auth-portal-01]

## <a name="next-steps"></a>次のステップ

* [Azure CLI を使用した最初のイメージのプッシュ](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
