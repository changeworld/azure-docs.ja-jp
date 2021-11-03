---
title: クイック スタート - Azure CLI を使用して App Service にサービス接続を作成する
description: Azure CLI を使用して App Service にサービス接続を作成する方法を示すクイック スタート
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9f702b28aebc15c9fb05986552e3b58d4a911c80
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091222"
---
# <a name="quickstart-create-a-service-connection-in-app-service-with-the-azure-cli"></a>クイック スタート: Azure CLI を使用して App Service にサービス接続を作成する

[Azure コマンド ライン インターフェイス (Azure CLI)](/cli/azure) は、Azure リソースを作成および管理するためのコマンド セットです。 Azure CLI は Azure サービス全体で利用でき、オートメーションに重点を置くことで、Azure で迅速に作業できるように設計されています。 このクイックスタートでは、Azure CLI を使用して Azure Web PubSub インスタンスを作成するためのオプションについて説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このクイックスタートには、Azure CLI のバージョン 2.22.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

- このクイックスタートは、少なくとも 1 つの App Service が Azure 上で既に実行されていることを前提としています。 App Service がない場合は、[作成します](../app-service/quickstart-dotnetcore.md)。


## <a name="view-supported-target-service-types"></a>サポートされているターゲット サービスの種類を表示する

Azure CLI の [az webapp connection]() コマンドを使用すると、App Service へのサービス接続を作成および管理できます。 

```azurecli-interactive
az webapp connection list-support-types
```

## <a name="create-a-service-connection"></a>サービス接続を作成する

Azure CLI の [az webapp connection]() コマンドで次の情報を指定すると、BLOB ストレージへのサービス接続を作成できます。

- **Source compute service resource group name:** App Service のリソース グループ名。
- **App Service name:** ターゲット サービスに接続する App Service の名前。
- **Target service resource group name:** BLOB ストレージのリソース グループ名。
- **Storage account name:** BLOB ストレージのアカウント名。

```azurecli-interactive
az webapp connection create storage-blob -g <app_service_resource_group> -n <app_service_name> --tg <storage_resource_group> --account <storage_account_name> --system-identity
```

> [!NOTE]
> BLOB ストレージがない場合は、`az webapp connection create storage-blob -g <app_service_resource_group> -n <app_service_name> --tg <storage_resource_group> --account <storage_account_name> --system-identity --new` を実行して新しいものをプロビジョニングし、お使いの App Service に直接接続することができます。

## <a name="view-connections"></a>接続の表示

Azure CLI の [az webapp connection]() コマンドで次の情報を指定すると、App Service への接続を一覧表示できます。

- **Source compute service resource group name:** App Service のリソース グループ名。
- **App Service name:** ターゲット サービスに接続する App Service の名前。

```azurecli-interactive
az webapp connection list -g "<your-app-service-resource-group>" --webapp "<your-app-service-name>"
```

## <a name="next-steps"></a>次のステップ

以下のチュートリアルに従って、Service Connector を使用する独自のアプリケーションの構築を開始してください。

> [!div class="nextstepaction"]
> [チュートリアル: Azure CLI を使用した WebApp + ストレージ](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [チュートリアル: Azure CLI を使用した WebApp + PostgreSQL](./tutorial-django-webapp-postgres-cli.md)
