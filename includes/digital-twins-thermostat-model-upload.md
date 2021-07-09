---
author: baanders
description: モデルを Azure Digital Twins インスタンスにアップロードするためのファイルを含める
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: 9fcbfc0b0fccb5f130f0c554bac93748176387b7
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111438983"
---
モデルは次のようになります。
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

**このモデルをツイン インスタンスにアップロードする** には、次の Azure CLI コマンドを実行します。これにより、上記のモデルがインライン JSON としてアップロードされます。 このコマンドは、ご利用のブラウザーで [Azure Cloud Shell](../articles/cloud-shell/overview.md) から実行できます (**Bash** 環境を使用)。CLI が[ローカルにインストール](/cli/azure/install-azure-cli)されている場合は、ご利用のコンピューター上でも実行できます。

```azurecli-interactive
az dt model create --dt-name <instance-name> --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' 
```