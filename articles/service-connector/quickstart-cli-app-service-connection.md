---
title: クイック スタート - Azure CLI を使用して App Service にサービス接続を作成する
description: Azure CLI を使用して App Service にサービス接続を作成する方法を示すクイック スタート
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d6d0f69f09f95694b718be17fe5567060d67c7b2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714715"
---
# <a name="quickstart-create-a-service-connection-in-app-service-with-the-azure-cli"></a>クイック スタート: Azure CLI を使用して App Service にサービス接続を作成する

[Azure コマンド ライン インターフェイス (Azure CLI)](/cli/azure) は、Azure リソースを作成および管理するためのコマンド セットです。 Azure CLI は Azure サービス全体で利用でき、オートメーションに重点を置くことで、Azure で迅速に作業できるように設計されています。 このクイックスタートでは、Azure CLI を使用して Azure Web PubSub インスタンスを作成するためのオプションについて説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このクイックスタートには、Azure CLI のバージョン 2.30.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

- このクイックスタートは、少なくとも 1 つの App Service が Azure 上で既に実行されていることを前提としています。 App Service がない場合は、[作成します](../app-service/quickstart-dotnetcore.md)。

## <a name="view-supported-target-service-types"></a>サポートされているターゲット サービスの種類を表示する

Azure CLI の [az webapp connection](/cli/azure/webapp/connection) コマンドを使用すると、App Service へのサービス接続を作成および管理できます。 

```azurecli-interactive
az provider register -n Microsoft.ServiceLinker
az webapp connection list-support-types --output table
```

## <a name="create-a-service-connection"></a>サービス接続を作成する

#### <a name="using-access-key"></a>[アクセス キーの使用](#tab/Using-access-key)

Azure CLI の [az webapp connection](/cli/azure/webapp/connection) コマンドで次の情報を指定すると、アクセス キーを使用した BLOB ストレージへのサービス接続を作成できます。

- **Source compute service resource group name:** App Service のリソース グループ名。
- **App Service name:** ターゲット サービスに接続する App Service の名前。
- **Target service resource group name:** BLOB ストレージのリソース グループ名。
- **Storage account name:** BLOB ストレージのアカウント名。

```azurecli-interactive
az webapp connection create storage-blob --secret
```

> [!NOTE]
> BLOB ストレージがない場合は、`az webapp connection create storage-blob --new --secret` を実行して新しいものをプロビジョニングし、お使いの App Service に直接接続することができます。

#### <a name="using-managed-identity"></a>[マネージド ID の使用](#tab/Using-Managed-Identity)

> [!IMPORTANT]
> マネージド ID を使用するには、[Azure AD ロールの割り当て](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)へのアクセス許可が必要です。 アクセス許可がない場合、接続の作成は失敗します。 接続を作成するために、サブスクリプション所有者にアクセス許可またはアクセス キーの使用を要求できます。

Azure CLI の [az webapp connection](/cli/azure/webapp/connection) コマンドで次の情報を指定すると、システム割り当てマネージド ID を使用した BLOB ストレージへのサービス接続を作成できます。

- **Source compute service resource group name:** App Service のリソース グループ名。
- **App Service name:** ターゲット サービスに接続する App Service の名前。
- **Target service resource group name:** BLOB ストレージのリソース グループ名。
- **Storage account name:** BLOB ストレージのアカウント名。

```azurecli-interactive
az webapp connection create storage-blob --system-identity
```

> [!NOTE]
> BLOB ストレージがない場合は、`az webapp connection create storage-blob --new --system-identity` を実行して新しいものをプロビジョニングし、お使いの App Service に直接接続することができます。

---

## <a name="view-connections"></a>接続の表示

Azure CLI の [az webapp connection](/cli/azure/webapp/connection) コマンドで次の情報を指定すると、App Service への接続を一覧表示できます。

- **Source compute service resource group name:** App Service のリソース グループ名。
- **App Service name:** ターゲット サービスに接続する App Service の名前。

```azurecli-interactive
az webapp connection list -g "<your-app-service-resource-group>" --webapp "<your-app-service-name>"
```

## <a name="next-steps"></a>次のステップ

以下のチュートリアルに従って、サービス コネクタを使用する独自のアプリケーションの構築を開始してください。

> [!div class="nextstepaction"]
> [チュートリアル: Azure CLI を使用した WebApp + ストレージ](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [チュートリアル: Azure CLI を使用した WebApp + PostgreSQL](./tutorial-django-webapp-postgres-cli.md)