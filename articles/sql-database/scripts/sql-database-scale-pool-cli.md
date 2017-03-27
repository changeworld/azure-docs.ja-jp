---
title: "Azure CLI スクリプト - エラスティック プールのスケール設定 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - エラスティック データベース プールのスケール設定"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: CLI
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/14/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 01e0c45556b67b844a4e4ab5e0eb8644e8673dd1
ms.lasthandoff: 03/10/2017

---

# <a name="scale-an-elastic-pool-in-azure-sql-database-using-the-azure-cli"></a>Azure CLI を使用して Azure SQL Database のエラスティック プールのスケールを設定する

この CLI のサンプル スクリプトは、エラスティック プールを作成し、プールされているデータベースを移動して、パフォーマンス レベルを変更します。 

このスクリプトを実行する前に、`az login` コマンドを使用して Azure との接続が作成されていることを確認してください。 

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows で実行する方法については、[Windows での Azure CLI の実行](../../virtual-machines/virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "プール間でのデータベースの移動")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用してリソース グループ、論理サーバー、SQL Database、ファイアウォール規則を作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | SQL Database をホストする論理サーバーを作成します。 |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | 論理サーバー内にエラスティック データベース プールを作成します。 |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | 論理サーバー内に SQL Database を作成します。 |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pools#update) | エラスティック データベース プールを更新します。このサンプルでは、割り当て済みの eDTU を変更します。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../sql-database-cli-samples.md)のページにあります。

