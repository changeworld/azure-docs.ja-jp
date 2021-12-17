---
author: trngo
description: az-cli を使用して個々のコンテナーを削除する方法について説明します
title: Fluid コンテナーを削除する
ms.author: trngo
ms.date: 09/28/2021
ms.service: azure-fluid
ms.topic: reference
ms.openlocfilehash: 8a94c06bd80b6997c5bd80cbb147bf68ccf91a4b
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728667"
---
# <a name="delete-fluid-containers-in-microsoft-azure-fluid-relay-server"></a>Microsoft Azure Fluid Relay サーバーの Fluid コンテナーを削除する

> [!NOTE]
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。

このシナリオでは、既存の Fluid コンテナーを削除します。 コンテナーが削除されると、そのコンテナーを参照しているアプリケーションでは、コンテナーまたはそのデータにアクセスできなくなります。 

## <a name="requirements-to-delete-a-fluid-container"></a>Fluid コンテナーを削除するための要件
- 最初に、[Azure CLI](/cli/azure/install-azure-cli) をインストールする必要があります。 Azure CLI が既にインストールされている場合は、`az version` を実行して、2.0.67 以上のバージョンであることを確認します。
- Fluid コンテナーを削除するには、アプリケーションとそのクライアントがコンテナーに接続されていないことを確認する必要があります。

## <a name="list-the-containers-within-a-fluid-relay-resource"></a>Fluid Relay リソース内のコンテナーを一覧表示する
Fluid Relay リソースに属しているすべてのコンテナーを表示するには、次のコマンドを実行します。
```
az rest --method get --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers?api-version=<apiVersion>
```
**subscriptionId**: Fluid Relay リソースが属している Azure サブスクリプションの ID。

**resourceGroupName**: Fluid Relay リソースがあるリソース グループの名前。

**frsResourceName**: Fluid Relay リソースの名前。 これは、Fluid Relay リソースの tenantId とは異なることに注意してください。

**apiVersion**: リソース プロバイダーの API バージョン。 サポートされる最低限のバージョンは **2021-08-30-preview** です。  


## <a name="sample-output"></a>サンプル出力
出力には、Fluid Relay リソースに属するコンテナーの一覧とそのプロパティが含まれています。
```json
{
  "value": [
    {
      "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.FluidRelay/fluidRelayServers/<frsResourcename>/fluidRelayContainers/<containerId>",
      "name": "<containerId>",
      "properties": {
        "frsContainerId": "<containerId>",
        "frsTenantId": "<frsResourceTenantId>"
      },
      "resourceGroup": "<resourceGroupname>",
      "type": "Microsoft.FluidRelay/fluidRelayServers/fluidRelayContainers"
    },
    ...
  ]
}
```


## <a name="delete-an-existing-container"></a>既存のコンテナーを削除する
コンテナーを削除するには、上記の出力からコンテナーの **containerId** を特定して、次のコマンドを実行する必要があります。 
```
az rest --method delete --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers/<frsContainerId>?api-version=<api-version>
```
  **frsContainerId**: 削除する Fluid コンテナーの ID。 
