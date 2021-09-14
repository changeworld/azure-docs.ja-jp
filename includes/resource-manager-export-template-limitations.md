---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/31/2021
ms.author: tomfitz
ms.openlocfilehash: 76c1ed0a2a1b1a2cca77988d218e2460f99003e6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436227"
---
## <a name="limitations"></a>制限事項

リソースグループまたはリソースからエクスポートする場合、エクスポートされたテンプレートは、リソースの種類ごとに [パブリッシュされたスキーマ](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) から生成されます。 場合によっては、スキーマにリソースの種類に対する最新バージョンがないことがあります。 エクスポートしたテンプレートに必要なプロパティが含まれていることを確認してください。 必要に応じて、エクスポートされたテンプレートを編集し、必要な API バージョンを使用します。

テンプレートのエクスポート機能は、Azure Data Factory リソースのエクスポートをサポートしていません。 Data Factory リソースをエクスポートする方法については、「[Azure Data Factory のデータ ファクトリをコピーまたは複製する](../articles/data-factory/copy-clone-data-factory.md)」を参照してください。

クラシック デプロイ モデルを使用して作成されたリソースをエクスポートするには、[Resource Manager デプロイ モデルに移行する](../articles/virtual-machines/migration-classic-resource-manager-overview.md)必要があります。

テンプレートをエクスポートしたとき、リソースの種類がエクスポートされなかったという警告が表示された場合でも、そのリソースのプロパティを検出できます。 リソースのプロパティについては、[テンプレート リファレンス](/azure/templates)を参照してください。 リソースの種類については、[Azure REST API](/rest/api/azure/) も参照できます。

エクスポートされたテンプレートを作成するリソース グループには、200 リソースという制限があります。 200 を超えるリソースを持つリソース グループをエクスポートしようとすると、エラー メッセージ `Export template is not supported for resource groups more than 200 resources` が表示されます。
