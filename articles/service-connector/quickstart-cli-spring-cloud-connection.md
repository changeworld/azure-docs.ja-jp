---
title: クイックスタート - Azure CLI を使用して Spring Cloud にサービス接続を作成する
description: Azure CLI を使用して Spring Cloud にサービス接続を作成する方法を示すクイックスタート
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4ee0fffb402ba3553055c732df9e0ebad94ea511
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091218"
---
# <a name="quickstart-create-a-service-connection-in-spring-cloud-with-the-azure-cli"></a>クイックスタート : Azure CLI を使用して Spring Cloud にサービス接続を作成する

[Azure コマンド ライン インターフェイス (Azure CLI)](/cli/azure) は、Azure リソースを作成および管理するためのコマンド セットです。 Azure CLI は Azure サービス全体で利用でき、オートメーションに重点を置くことで、Azure で迅速に作業できるように設計されています。 このクイックスタートでは、Azure CLI を使用して Azure Web PubSub インスタンスを作成するためのオプションについて説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このクイックスタートには、Azure CLI のバージョン 2.22.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

- このクイックスタートは、少なくとも 1 つの Spring Cloud アプリケーションが Azure 上で既に実行されていることを前提としています。 Spring Cloud アプリケーションがない場合は、[作成します](../spring-cloud/quickstart.md)。


## <a name="view-supported-target-service-types"></a>サポートされているターゲット サービスの種類を表示する

Azure CLI の [az spring-cloud connection]() コマンドを使用して、Spring Cloud アプリケーションへのサービス接続を作成および管理します。 

```azurecli-interactive
az spring-cloud connection list-support-types
```

## <a name="create-a-service-connection"></a>サービス接続を作成する

Azure CLI の [az spring-cloud connection]() コマンドで次の情報を指定して、BLOB ストレージへのサービス接続を作成します。

- **Spring Cloud resource group name:** Spring Cloud のリソース グループ名。
- **Spring Cloud name:** ご使用の Spring Cloud の名前。
- **Spring Cloud app name:** ターゲット サービスに接続する Spring Cloud アプリの名前。
- **Target service resource group name:** BLOB ストレージのリソース グループ名。
- **Storage account name:** BLOB ストレージのアカウント名。

```azurecli-interactive
az spring-cloud connection create storage-blob -g <spring_cloud_resource_group> --service <spring_cloud_name> --app <app_name> --deployment default --tg <storage_resource_group> --account <storage_account_name> --system-identity
```

> [!NOTE]
> BLOB ストレージがない場合は、`az spring-cloud connection create storage-blob -g <app_service_resource_group> -n <app_service_name> --tg <storage_resource_group> --account <storage_account_name> --system-identity --new` を実行して新しいものをプロビジョニングし、お使いの App Service に直接接続することができます。

## <a name="view-connections"></a>接続の表示

Azure CLI の [az spring-cloud connection]() コマンドで次の情報を指定して、Spring Cloud アプリケーションへのサービス接続を表示します。

```azurecli-interactive
az spring-cloud connection list -g <your-spring-cloud-resource-group> --spring-cloud <your-spring-cloud-name>
```

## <a name="next-steps"></a>次のステップ

以下のチュートリアルに従って、サービス コネクタを使用する独自のアプリケーションの構築を開始してください。

> [!div class="nextstepaction"]
> [チュートリアル: Spring Cloud + MySQL](./tutorial-java-spring-mysql.md)

> [!div class="nextstepaction"]
> [チュートリアル: Confluent Cloud での Spring Cloud + Apache Kafka](./tutorial-java-spring-confluent-kafka.md)
