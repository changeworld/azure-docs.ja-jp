---
title: CLI を使用して Azure Data Explorer クラスターとデータベースを作成する
description: この記事では、Azure CLI を使用して、Azure Data Explorer クラスターとデータベースを作成する方法について説明します
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: howto
ms.date: 1/31/2019
ms.openlocfilehash: a4c9156ef80f05e247b1cfef0acd56b601a2db65
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812686"
---
# <a name="create-an-azure-data-explorer-cluster-and-a-database-using-cli"></a>CLI を使用して Azure Data Explorer クラスターとデータベースを作成する

この記事では、Azure CLI を使用して、Azure Data Explorer クラスターとデータベースを作成する方法について説明します。

## <a name="whats-the-difference-between-the-management-plane-and-data-plane-apis"></a>管理プレーンとデータ プレーンの API の違い

管理およびデータ プレーン API という 2 つの異なる API ライブラリがあります。
管理 API はリソースの管理に使用されます。たとえば、クラスターやデータベースの作成、データ接続の削除、インスタンス カウント数の変更などです。データ プレーン API は、データの操作、クエリの実行、データの取り込みなどに使用されます。

## <a name="configure-the-cli-parameters"></a>CLI パラメーターを構成する

アカウントへのログイン

```Bash
az login
```

クラスターを作成するサブスクリプションを設定します。

```Bash
az account set --subscription "your_subscription"
```

## <a name="create-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターを作成する

次のコマンドを使用して、クラスターを作成します。

```Bash
az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
```

次の値を指定します

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | name | *azureclitest* | クラスターの任意の名前。|
   | sku | *D13_v2* | クラスターに使用される SKU。 |
   | resource-group | *testrg* | クラスターが作成されるリソース グループの名前。 |

必要に応じて、クラスターの容量など、省略可能なパラメーターをさらに使用できます。

クラスターが正常に作成されたかどうかを確認する場合は、以下を実行できます

```Bash
az kusto cluster show --name azureclitest --resource-group testrg
```

結果に値が "Succeeded" の "provisioningState" が含まれている場合、クラスターが正常に作成されたことを意味します。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターでデータベースを作成する

次のコマンドを使用して、データベースを作成します。

```Bash
az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
```

次の値を指定します

   |**設定** | **推奨値** | **フィールドの説明**|
   |---|---|---|
   | cluster-name | *azureclitest* | 作成する必要があるクラスターの名前。|
   | name | *clidatabase* | データベースの任意の名前。|
   | resource-group | *testrg* | クラスターが作成されるリソース グループの名前。 |
   | soft-delete-period | *3650:00:00:00* | データをクエリに使用できるようにするために保持する必要がある時間。 |
   | hot-cache-period | *3650:00:00:00* | データをキャッシュで保持する必要がある時間。 |

以下を実行して、作成したデータベースを確認できます

```Bash
az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
```

これで、クラスターとデータベースが作成されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

他のクイック スタートやチュートリアルを行う場合は、作成したリソースをそのままにします。

クラスターを削除するときに、その中に含まれるデータベースもすべて削除されます。 以下のコマンドを使用して、クラスターを削除します。

```Bash
az kusto cluster delete --name azureclitest --resource-group testrg
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クイック スタート:イベント ハブから Azure Data Explorer にデータを取り込む](ingest-data-event-hub.md)
