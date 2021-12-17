---
title: REST APIを管理および監視するのにアプリケーションの活用には、 IoT Centralを使用|Microsoft Docs
description: この記事では、アプリケーションを使用してアプリケーションを作成IoT Central管理する方法について説明REST API。 このREST API、システム割り当てマネージド ID をアプリケーションに追加できます。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/25/2021
ms.topic: how-to
ms.openlocfilehash: a4df2346346d048bcd4e01e898636c1668720c4c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092747"
---
# <a name="use-the-rest-api-to-create-and-manage-iot-central-applications"></a>アプリケーションをREST APIして管理するには、 IoT Central使用します

[REST APIコントロール プレーン ](/rest/api/iotcentral/2021-06-01controlplane/apps) を 使用してアプリケーションを作成IoT Centralできます。 アプリケーションにマネージド ID のREST APIを使用して追加できます。

この API を使用するには、`management.azure.com`リソースのベアラー トークンが必要  です。 ベアラー トークンを取得するには、次のコマンドを使用Azure CLI：

```azurecli
az account get-access-token --resource https://management.azure.com
```

## <a name="list-your-applications"></a>アプリケーションを一覧表示する

サブスクリプション内のアプリケーションのIoT Central一覧を取得するには:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.IoTCentral/iotApps?api-version=2021-06-01
```

リソース グループ内のアプリケーションIoT Central一覧を取得するには:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.IoTCentral/iotApps?api-version=2021-06-01
```

個々のアプリケーションの詳細を取得できます：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.IoTCentral/iotApps/{applicationName}?api-version=2021-06-01
```

## <a name="create-an-iot-central-application"></a>IoT Central アプリケーションを作成する

システム割り当IoT Central[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を持つアプリケーションを 作成するには:

```http
PUT https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

次のペイロードは、マネージド ID を含む新しいアプリケーションの構成を示しています：

```json
{
  "location": "eastus",
  "sku": {
    "name": "ST2"
  },
  "properties": {
    "displayName": "Contoso IoT Central App",
    "subdomain": "my-iot-central-app",
    "template": "iotc-pnp-preview@1.0.0"
  },
  "identity": {
    "type": "SystemAssigned"
  }
}
```

## <a name="modify-an-iot-central-application"></a>アプリケーションのIoT Central変更

既存のアプリケーションを変更IoT Centralできます。 フォローの例えば、表示名を変更し、システム割り当てマネージド ID を有効にする方法を示しています。

```http
PATCH https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

表示名を変更し、システム割り当てマネージド ID を有効にするには、次のペイロードを使用します。

```json
{
  "properties": {
    "displayName": "Contoso IoT Central App"
  },
  "identity": {
    "type": "SystemAssigned"
  }
}
```

> [!NOTE]
> マネージド ID は、リージョンに作成IoT Centralアプリケーションにのみ追加できます。 すべての新しいアプリケーションがリージョンに作成されます。 詳細については、[「更新プログラム」](https://azure.microsoft.com/updates/azure-iot-central-new-and-updated-features-august-2021/)を 参照してください。

## <a name="delete-an-iot-central-application"></a>アプリケーションをIoT Centralする

アプリケーションを削除するにはIoT Central使用します。

```http
DELETE https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

## <a name="next-steps"></a>次の手順

次に、REST API を使用して Azure IoT Central アプリケーションを作成および管理する方法を学習しました。推奨される次の手順を次に示します。

> [!div class="nextstepaction"]
> [IoT Central REST API を使用してユーザーとロールを管理する方法](howto-manage-users-roles-with-rest-api.md)