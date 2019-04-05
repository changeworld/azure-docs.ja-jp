---
title: Azure Container Registry - ロールとアクセス許可
description: Azure のロールベースのアクセス制御 (RBAC) と ID およびアクセス管理 (IAM) を使用して、Azure コンテナー レジストリ内のリソースへのきめ細かいアクセス許可を提供します。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/20/2019
ms.author: danlep
ms.openlocfilehash: 0148894bb013dc9f8cce595f14919f87d6292df8
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593626"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry のロールとアクセス許可

Azure Container Registry サービスでは、Azure コンテナー レジストリにさまざまなレベルのアクセス許可を提供する Azure のロールのセットがサポートされています。 Azure の[ロールベースのアクセス制御](../role-based-access-control/index.yml) (RBAC) を使用して、レジストリと対話する必要のあるユーザーまたはサービス プリンシパルに特定のアクセス許可を割り当てます。

| ロール/アクセス許可       | [Resource Manager へのアクセス](#access-resource-manager) | [レジストリの作成/削除](#create-and-delete-registry) | [イメージのプッシュ](#push-image) | [イメージのプル](#pull-image) | [イメージ データを削除する](#delete-image-data) | [ポリシーの変更](#change-policies) |   [イメージの署名](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Owner | X | X | X | X | X | X |  |  
| Contributor | X | X | X |  X | X | X |  |  
| Reader | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | X |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>ユーザーとサービスを区別する

アクセス許可が適用されるときのベスト プラクティスは常に、タスクを実行できる範囲で最も限定的なアクセス許可のセットを人またはサービスに提供することです。 以下のアクセス許可セットでは、人およびヘッドレス サービスで使用できる機能のセットが示されています。

### <a name="cicd-solutions"></a>CI/CD ソリューション

CI/CD ソリューションから `docker build` コマンドを自動化するときは、`docker push` の機能が必要です。 これらのヘッドレス サービス シナリオでは、**AcrPush** ロールを割り当てることをお勧めします。 このロールでは、さらに範囲の広い**共同作成者**ロールとは異なり、アカウントによる他のレジストリ操作の実行または Azure Resource Manager へのアクセスは禁止されます。

### <a name="container-host-nodes"></a>コンテナー ホスト ノード

同様に、コンテナーを実行するノードでは、**AcrPull** ロールは必要ですが、**閲覧者**の機能は必要ないはずです。

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker 拡張機能

Visual Studio Code [Docker 拡張機能](https://code.visualstudio.com/docs/azure/docker)などのツールでは、使用可能な Azure コンテナー レジストリの一覧を取得するために、追加のリソース プロバイダー アクセスが必要です。 この場合は、**閲覧者**または**共同作成者**ロールへのアクセスをユーザーに提供します。 これらのロールでは、`docker pull`、`docker push`、`az acr list`、`az acr build`、およびその他の機能が許可されます。 

## <a name="access-resource-manager"></a>Resource Manager へのアクセス

Azure portal と [Azure CLI](/cli/azure/) によるレジストリ管理には、Azure Resource Manager へのアクセスが必要です。 たとえば、`az acr list` コマンドを使用してレジストリの一覧を取得するには、このアクセス許可セットが必要です。 

## <a name="create-and-delete-registry"></a>レジストリの作成と削除

Azure コンテナー レジストリを作成および削除する権限です。

## <a name="push-image"></a>イメージのプッシュ

レジストリに対して、イメージの `docker push` を行う、または Helm Chart などの別の[サポートされている成果物](container-registry-image-formats.md)をプッシュする権限です。 承認された ID を使用してレジストリで[認証](container-registry-authentication.md)を行う必要があります。 

## <a name="pull-image"></a>イメージのプル

レジストリから検疫されていないイメージの `docker pull` を行う、または Helm Chart などの別の[サポートされている成果物](container-registry-image-formats.md)をプルする権限です。 承認された ID を使用してレジストリで[認証](container-registry-authentication.md)を行う必要があります。

## <a name="delete-image-data"></a>イメージ データを削除する

[コンテナー イメージまたはリポジトリを削除する](container-registry-delete.md)権限です。

## <a name="change-policies"></a>ポリシーの変更

レジストリでポリシーを構成する権限です。 ポリシーには、イメージの削除、検疫の有効化、イメージの署名が含まれます。

## <a name="sign-images"></a>イメージの署名

イメージに署名する権限であり、通常は、サービス プリンシパルを使用する自動化されたプロセスに割り当てられます。 通常、このアクセス許可は、レジストリへの信頼できるイメージのプッシュを許可する[イメージのプッシュ](#push-image)と組み合わされます。 詳細については、「[Azure Container Registry におけるコンテンツの信頼](container-registry-content-trust.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* [Azure portal](../role-based-access-control/role-assignments-portal.md)、[Azure CLI](../role-based-access-control/role-assignments-cli.md)、またはその他の Azure ツールを使用した Azure ID への RBAC ロールの割り当てについてさらに理解します。

* Azure Container Registry の[認証オプション](container-registry-authentication.md)について理解します。
