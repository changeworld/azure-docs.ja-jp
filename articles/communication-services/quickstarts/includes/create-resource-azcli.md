---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: 06e37015bb95c3bad54f7eb46b7dd8991b6b05bb
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966483"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/dotnet/)。
- [Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli) 

## <a name="create-azure-communication-services-resource"></a>Azure Communication Services のリソースを作成する

Azure Communication Services のリソースを作成するには、[Azure CLI にサインイン](/cli/azure/authenticate-azure-cli)します。 そのためにはターミナルから ```az login``` コマンドを使用し、資格情報を入力します。 リソースを作成するには、次のコマンドを実行します。

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

特定のサブスクリプションを選択したい場合は、```--subscription``` フラグを指定してサブスクリプション ID を指定することもできます。
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

以下のオプションを使用して、Communication Services のリソースを構成できます。

* リソース グループ
* Communication Services のリソースの名前
* リソースが関連付けられる地理的な場所

次の手順では、リソースにタグを割り当てることができます。 タグは、Azure リソースを整理するために使用できます。 タグの詳細については、[リソースのタグ付けに関するドキュメント](../../../azure-resource-manager/management/tag-resources.md)を参照してください。

## <a name="manage-your-communication-services-resource"></a>Communication Services のリソースを管理する

Communication Services のリソースにタグを追加するには、以下のコマンドを実行します。 また、特定のサブスクリプションを対象にすることもできます。

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

その他のコマンドについては、「[az communication](/cli/azure/communication)」を参照してください。