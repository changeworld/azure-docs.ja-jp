---
title: "Azure CLI スクリプト - SQL データベースの作成 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Azure CLI を使用した SQL データベースの作成"
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
ms.openlocfilehash: 4a7343ebc5090dc82f6c7b470c697167536315ca
ms.lasthandoff: 03/10/2017

---

# <a name="create-a-single-sql-database-and-configure-a-firewall-rule-using-the-azure-cli"></a>Azure CLI を使用して SQL データベースを&1; つ作成しファイアウォール規則を構成する

この CLI のサンプル スクリプトでは、Azure SQL データベースを作成し、サーバー レベルのファイアウォール規則を構成します。 スクリプトが正常に実行されると、すべての Azure サービスおよび構成済み IP アドレスから SQL Database へアクセスできるようになります。 

このスクリプトを実行する前に、`az login` コマンドを使用して Azure との接続が作成されていることを確認してください。 

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows で実行する方法については、[Windows での Azure CLI の実行](../../virtual-machines/virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "SQL データベースの作成")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az sql server create](/cli/azure/sql/server#create) | SQL Database をホストする論理サーバーを作成します。 |
| [az sql server firewall create](/cli/azure/sql/server/firewall#create) | サーバー上のすべての SQL データベースに対する入力した IP アドレス範囲からのアクセスを許可するファイアウォール規則を作成します。 |
| [az sql db create](/cli/azure/sql/db#create) | 論理サーバー内に SQL Database を作成します。 |
| [az group delete](/cli/azure/resource#delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../sql-database-cli-samples.md)のページにあります。


