---
author: baanders
description: モデルを Azure Digital Twins インスタンスにアップロードするためのファイルを含める
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304301"
---
モデルは次のようになります。
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

**このモデルをツイン インスタンスにアップロードする** には、次の Azure CLI コマンドを実行します。これにより、上記のモデルがインライン JSON としてアップロードされます。 このコマンドは、ご利用のブラウザーで [Azure Cloud Shell](../articles/cloud-shell/overview.md) から実行できます (**Bash** 環境を使用)。CLI が[ローカルにインストール](/cli/azure/install-azure-cli)されている場合はご利用のコンピューター上でも実行できます。

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```