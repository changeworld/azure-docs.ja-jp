---
title: クイック スタート:CLI を使用して Azure Data Explorer クラスターとデータベースを作成する
description: Azure CLI を使用して Azure Data Explorer クラスターとデータベースを作成する方法を学習します
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: ca4ffce1ae966d2bb8fb187c9b04938330969641
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286331"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-the-cli"></a>CLI を使用して Azure Data Explorer クラスターとデータベースを作成する

> [!div class="op_single_selector"]
> * [ポータル](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

このクイック スタートでは、Azure CLI を使用して、Azure Data Explorer クラスターとデータベースを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Azure サブスクリプションが必要です。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI をローカルにインストールして使用する場合、このクイック スタートでは、Azure CLI バージョン 2.0.4 以降を実行する必要があります。 バージョンを確認するには `az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。

## <a name="configure-the-cli-parameters"></a>CLI パラメーターを構成する

Azure Cloud Shell でコマンドを実行している場合、次の手順は必要ありません。 ローカル環境で CLI を実行している場合、次の手順に従って Azure にサインインし、現在のサブスクリプションを設定します。

1. 次のコマンドを実行して、Azure にサインインします。

    ```azurecli-interactive
    az login
    ```

2. クラスターを作成するサブスクリプションを設定します。 `MyAzureSub` は、使用する Azure サブスクリプションの名前に置き換えてください。

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターを作成する

1. 次のコマンドを使用して、クラスターを作成します。

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | name | *azureclitest* | クラスターの任意の名前。|
   | sku | *D13_v2* | クラスターに使用される SKU。 |
   | resource-group | *testrg* | クラスターが作成されるリソース グループの名前。 |

    使用できる省略可能なパラメーターが他にも存在します (クラスターの容量など)。

2. クラスターが正常に作成されたかどうかを確認するには、次のコマンドを実行します。

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

結果に値が `Succeeded` の `provisioningState` が含まれている場合、クラスターは正常に作成されています。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターでデータベースを作成する

1. 次のコマンドを使用して、データベースを作成します。

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | cluster-name | *azureclitest* | データベースの作成先となるクラスターの名前。|
   | name | *clidatabase* | データベースの名前。|
   | resource-group | *testrg* | クラスターが作成されるリソース グループの名前。 |
   | soft-delete-period | *3650:00:00:00* | データをクエリに使用できるようにしておく時間。 |
   | hot-cache-period | *3650:00:00:00* | データをキャッシュに保持する時間。 |

2. 次のコマンドを実行して、作成したデータベースを確認します。

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

クラスターとデータベースが作成されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

* 他のクイック スタートやチュートリアルを行う場合は、作成したリソースをそのままにします。
* リソースをクリーンアップするには、クラスターを削除します。 クラスターを削除するときに、その中に含まれるデータベースもすべて削除されます。 クラスターを削除するには次のコマンドを使います。

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クイック スタート:Azure Data Explorer の Python ライブラリを使用してデータを取り込む](python-ingest-data.md)
