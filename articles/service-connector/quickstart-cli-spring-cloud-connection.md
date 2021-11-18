---
title: クイックスタート - Azure CLI を使用して Spring Cloud にサービス接続を作成する
description: Azure CLI を使用して Spring Cloud にサービス接続を作成する方法を示すクイックスタート
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7a1b93208dc52466af00095a75366a8f5d078200
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709615"
---
# <a name="quickstart-create-a-service-connection-in-spring-cloud-with-the-azure-cli"></a>クイックスタート : Azure CLI を使用して Spring Cloud にサービス接続を作成する

[Azure コマンド ライン インターフェイス (Azure CLI)](/cli/azure) は、Azure リソースを作成および管理するためのコマンド セットです。 Azure CLI は Azure サービス全体で利用でき、オートメーションに重点を置くことで、Azure で迅速に作業できるように設計されています。 このクイックスタートでは、Azure CLI を使用して Azure Web PubSub インスタンスを作成するためのオプションについて説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このクイックスタートには、Azure CLI のバージョン 2.30.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

- このクイックスタートは、少なくとも 1 つの Spring Cloud アプリケーションが Azure 上で既に実行されていることを前提としています。 Spring Cloud アプリケーションがない場合は、[作成します](../spring-cloud/quickstart.md)。


## <a name="view-supported-target-service-types"></a>サポートされているターゲット サービスの種類を表示する

Azure CLI の [az spring-cloud connection]() コマンドを使用して、Spring Cloud アプリケーションへのサービス接続を作成および管理します。 

```azurecli-interactive
az provider register -n Microsoft.ServiceLinker
az spring-cloud connection list-support-types --output table
```

## <a name="create-a-service-connection"></a>サービス接続を作成する

#### <a name="using-access-key"></a>[アクセス キーの使用](#tab/Using-access-key)

Azure CLI の [az spring-cloud connection]() コマンドで次の情報を指定すると、アクセス キーを使用した BLOB ストレージへのサービス接続を作成できます。

- **Spring Cloud resource group name:** Spring Cloud のリソース グループ名。
- **Spring Cloud name:** ご使用の Spring Cloud の名前。
- **Spring Cloud app name:** ターゲット サービスに接続する Spring Cloud アプリの名前。
- **Target service resource group name:** BLOB ストレージのリソース グループ名。
- **Storage account name:** BLOB ストレージのアカウント名。

```azurecli-interactive
az spring-cloud connection create storage-blob --secret
```

> [!NOTE]
> BLOB ストレージがない場合は、`az spring-cloud connection create storage-blob --new --secret` を実行して新しいものをプロビジョニングし、お使いの App Service に直接接続することができます。

#### <a name="using-managed-identity"></a>[マネージド ID の使用](#tab/Using-Managed-Identity)

> [!IMPORTANT]
> マネージド ID を使用するには、[Azure AD ロールの割り当て](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)へのアクセス許可が必要です。 アクセス許可がない場合、接続の作成は失敗します。 接続を作成するために、サブスクリプション所有者にアクセス許可またはアクセス キーの使用を要求できます。

Azure CLI の [az spring-cloud connection]() コマンドで次の情報を指定すると、システム割り当てマネージド ID を使用した BLOB ストレージへのサービス接続を作成できます。

- **Spring Cloud resource group name:** Spring Cloud のリソース グループ名。
- **Spring Cloud name:** ご使用の Spring Cloud の名前。
- **Spring Cloud app name:** ターゲット サービスに接続する Spring Cloud アプリの名前。
- **Target service resource group name:** BLOB ストレージのリソース グループ名。
- **Storage account name:** BLOB ストレージのアカウント名。

```azurecli-interactive
az spring-cloud connection create storage-blob --system-identity
```

> [!NOTE]
> BLOB ストレージがない場合は、`az spring-cloud connection create --system-identity --new --secret` を実行して新しいものをプロビジョニングし、お使いの App Service に直接接続することができます。

---

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