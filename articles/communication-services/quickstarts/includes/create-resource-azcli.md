---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: 32d869e6285e76b57a7b8e462e8110a43de82dd9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656579"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet/)。

## <a name="create-azure-communication-resource"></a>Azure Communication のリソースを作成する

Azure Communication Services のリソースを作成するには、[Azure CLI にサインイン](/cli/azure/authenticate-azure-cli)してから、次のコマンドを実行します。

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

以下のオプションを使用して、Communication Services のリソースを構成できます。

* リソース グループ
* Communication Services のリソースの名前
* リソースが関連付けられる地理的な場所

次の手順では、リソースにタグを割り当てることができます。 タグは、Azure リソースを整理するために使用できます。 タグの詳細については、[リソースのタグ付けに関するドキュメント](../../../azure-resource-manager/management/tag-resources.md)を参照してください。

## <a name="manage-your-communication-services-resource"></a>Communication Services のリソースを管理する

Communication Services のリソースにタグを追加するには、以下のコマンドを実行します。

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

その他のコマンドについては、「[az communication](/cli/azure/ext/communication/communication)」を参照してください。