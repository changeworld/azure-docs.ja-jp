---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/16/2021
ms.author: danlep
ms.openlocfilehash: a5bfee2b0348a68476855edc2b113338b779f20d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029702"
---
## <a name="import-images-to-your-cloud-registry"></a>クラウド レジストリにイメージをインポートする

[az acr import](/cli/azure/acr#az_acr_import) コマンドを使用して、次のコンテナー イメージをクラウド レジストリにインポートします。 これらのイメージを既にインポートしている場合は、この手順をスキップします。

### <a name="connected-registry-image"></a>接続されたレジストリのイメージ

入れ子になった IoT Edge のシナリオをサポートするには、接続されたレジストリのランタイムのコンテナー イメージがプライベート Azure コンテナー レジストリで使用できる必要があります。 接続されたレジストリのイメージをプライベート レジストリにインポートするには、[az acr import](/cli/azure/acr#az_acr_import) コマンドを使用します。 

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/acr/connected-registry:0.5.0
```

### <a name="iot-edge-and-api-proxy-images"></a>IoT Edge と API プロキシのイメージ

入れ子になった IoT Edge で接続されたレジストリをサポートするには、IoT Edge と API プロキシのモジュールをデプロイする必要があります。 これらのイメージをプライベート レジストリにインポートします。

[IoT Edge API プロキシ モジュール](../articles/iot-edge/how-to-configure-api-proxy-module.md)を使用すると、同じポート (443 など) で HTTPS プロトコルを使用して複数のサービスを公開できます。

```azurecli
az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-agent:1.2.4

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-hub:1.2.4

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-api-proxy:1.1.2

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-diagnostics:1.2.4
```

### <a name="hello-world-image"></a>Hello-world イメージ

接続されたレジストリをテストするには、`hello-world` イメージをインポートします。 このリポジトリは、接続されたレジストリに同期され、接続されたレジストリのクライアントによってプルされます。

```azurecli
az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/hello-world:1.1.2
```