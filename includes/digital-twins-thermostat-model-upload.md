---
author: baanders
description: モデルを Azure Digital Twins インスタンスにアップロードするためのファイルを含める
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: 3a9ec169f91ebe048914c3ef2163e4fde7485389
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2021
ms.locfileid: "109783676"
---
モデルは次のようになります。
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

**このモデルをツイン インスタンスにアップロードする** には、次の Azure CLI コマンドを実行します。これにより、上記のモデルがインライン JSON としてアップロードされます。 このコマンドは、ご利用のブラウザーで [Azure Cloud Shell](../articles/cloud-shell/overview.md) から実行できます (**Bash** 環境を使用)。CLI が [ローカルにインストール](/cli/azure/install-azure-cli)されている場合はご利用のコンピューター上でも実行できます。

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' --dt-name {digital_twins_instance_name}
```