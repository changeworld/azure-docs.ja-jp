---
title: CLI の例 - Azure SQL Database にマネージド インスタンスを作成する
description: Azure SQL Database にマネージド インスタンスを作成する Azure CLI スクリプトの例です
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: f52a460be33b23da3da0cb32075ab5f75bb8d129
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772626"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>CLI を使用して Azure SQL Database のマネージド インスタンスを作成する

この Azure CLI サンプル スクリプトでは、新しい仮想ネットワーク内の専用サブネットに Azure SQL Database マネージド インスタンスを作成します。 また、仮想ネットワークのルート テーブルとネットワーク セキュリティ グループも構成します。 スクリプトが正常に実行されると、マネージド インスタンスに仮想ネットワーク内またはオンプレミス環境からアクセスできます。 「[Azure SQL Database Managed Instance に接続するように Azure VM を構成する](../sql-database-managed-instance-configure-vm.md)」と「[オンプレミスから Azure SQL Database Managed Instance へのポイント対サイト接続を構成する](../sql-database-managed-instance-configure-p2s.md)」を参照してください。

> [!IMPORTANT]
> 制限については、[サポートされているリージョン](../sql-database-managed-instance-resource-limits.md#supported-regions)と[サポートされているサブスクリプションの種類](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)に関するセクションを参照してください。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>スクリプトを実行する

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>サンプル リファレンス

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| | |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | 仮想ネットワークのコマンド。 |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | 仮想ネットワーク サブネットのコマンド。 |
| [az network route-table](/cli/azure/network/route-table) | ネットワーク ルート テーブルのコマンド。 |
| [az sql mi](/cli/azure/sql/mi) | マネージド インスタンスのコマンド。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../sql-database-cli-samples.md)のページにあります。
