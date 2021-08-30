---
author: baanders
description: Azure 関数を Azure Digital Twins と連動するよう構成する方法を説明するインクルード ファイル - CLI の手順
ms.service: digital-twins
ms.topic: include
ms.date: 7/20/2021
ms.author: baanders
ms.openlocfilehash: fffa878ca69b0704417c7db7492eb5523217e4e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722339"
---
[Azure Cloud Shell](https://shell.azure.com) または[ローカル Azure CLI](/cli/azure/install-azure-cli) で次のコマンドを実行します。

#### <a name="assign-an-access-role"></a>アクセス ロールを割り当てる

Azure 関数には、ベアラー トークンを渡す必要があります。 ベアラー トークンが確実に渡されるようにするには、関数アプリに Azure Digital Twins インスタンスの **Azure Digital Twins データ所有者** ロールを付与します。これにより、インスタンスでデータ プレーン アクティビティを実行するアクセス許可が関数アプリに付与されます。

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

1. 次のコマンドを使用して、関数の[システム マネージド ID](../articles/active-directory/managed-identities-azure-resources/overview.md) の詳細を確認します。 出力の `principalId` フィールドを書き留めてください。 この ID は、次の手順でアクセス許可を付与できるように、関数を参照するために使用します。

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-function-app-name> 
    ```

    >[!NOTE]
    > 結果が空で、ID の詳細が表示されない場合は、次のコマンドを使用して、関数の新しいシステム マネージド ID を作成します。
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-function-app-name>  
    >```
    >
    > 出力には、次の手順に必要な `principalId` 値を含む、ID の詳細が表示されます。 

1. 次のコマンドで `principalId` 値を使用して、対象の Azure Digital Twins インスタンスの **Azure Digital Twins データ所有者** ロールを関数に付与します。

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

#### <a name="configure-application-settings"></a>アプリケーション設定の構成

次に、[環境変数](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal#use-application-settings)を設定することで、関数から **Azure Digital Twins インスタンス** の URL にアクセスできるようにします。

> [!TIP]
> Azure Digital Twins インスタンスの URL を作成するには、インスタンスのホスト名の先頭に *https://* を追加します。 インスタンスのすべてのプロパティと共にホスト名を表示するには、`az dt show --dt-name <your-Azure-Digital-Twins-instance>` を実行します。

次のコマンドでは、関数がインスタンスにアクセスする必要があるときに必ず使用する、対象のインスタンスの URL の環境変数を設定します。

```azurecli-interactive 
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```