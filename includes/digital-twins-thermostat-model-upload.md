---
author: baanders
description: モデルを Azure Digital Twins インスタンスにアップロードするためのファイルを含める
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950598"
---
モデルは次のようになります。
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

**このモデルをツイン インスタンスにアップロードする** には、次の Azure CLI コマンドを実行します。これにより、上記のモデルがインライン JSON としてアップロードされます。 このコマンドは、ご利用のブラウザーで [Azure Cloud Shell](../articles/cloud-shell/overview.md) から実行できます。CLI が [ローカルにインストールされている](/cli/azure/install-azure-cli)場合はご利用のコンピューター上でも実行できます。

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> PowerShell 環境で Cloud Shell を使用している場合は、値が正しく解析されるようにするために、インライン JSON フィールド上で引用符文字をエスケープすることが必要になる場合があります。 この変更でモデルをアップロードするコマンドを次に示します。
>
> モデルのアップロード:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```