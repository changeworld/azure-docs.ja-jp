---
title: Azure CLI:マネージド インスタンスを作成する
description: Azure SQL Managed Instance にマネージド インスタンスを作成する Azure CLI スクリプトの例です
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
ms.openlocfilehash: 9d0058fd90590b10a0d5745bce1875db9ad35745
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518896"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>CLI を使用して Azure SQL Managed Instance を作成する

この Azure CLI サンプル スクリプトは、新しい仮想ネットワーク内の専用サブネットに Azure SQL Managed Instance を作成します。 また、仮想ネットワークのルート テーブルとネットワーク セキュリティ グループも構成します。 スクリプトが正常に実行されると、マネージド インスタンスに仮想ネットワーク内またはオンプレミス環境からアクセスできます。 [Azure SQL Managed Instance に接続するように Azure VM を構成する](../../azure-sql/managed-instance/connect-vm-instance-configure.md)方法に関するページと、[オンプレミスから Azure SQL マネージド インスタンスへのポイント対サイト接続を構成する](../../azure-sql/managed-instance/point-to-site-p2s-configure.md)方法に関するページを参照してください。

> [!IMPORTANT]
> 制限については、[サポートされているリージョン](../../azure-sql/managed-instance/resource-limits.md#supported-regions)と[サポートされているサブスクリプションの種類](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types)に関するセクションを参照してください。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>スクリプトを実行する

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>デプロイのクリーンアップ

リソース グループと、それに関連付けられているすべてのリソースを削除するには、次のコマンドを使用します。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>サンプル リファレンス

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | 説明 |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | 仮想ネットワークのコマンド。 |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | 仮想ネットワーク サブネットのコマンド。 |
| [az network route-table](/cli/azure/network/route-table) | ネットワーク ルート テーブルのコマンド。 |
| [az sql mi](/cli/azure/sql/mi) | SQL Managed Instance のコマンド。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../../azure-sql/database/az-cli-script-samples-content-guide.md)のページにあります。
