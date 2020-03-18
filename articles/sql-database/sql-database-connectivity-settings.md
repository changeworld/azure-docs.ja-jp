---
title: Azure SQL Database と Data Warehouse の接続の設定
description: このドキュメントでは、Azure SQL の TLS バージョンの選択と、プロキシとリダイレクトの設定について説明します
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096682"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL の接続の設定
> [!NOTE]
> この記事は Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。

> [!IMPORTANT]
> この記事は、**Azure SQL Database Managed Instance** には適用*されません*。

この記事では、Azure SQL Database への接続をサーバー レベルで制御する設定について説明します。 これらの設定は、サーバーに関連する**すべての** SQL Database および SQL Data Warehouse データベースに適用されます。

> [!NOTE]
> これらの設定を適用すると、それらは**直ちに有効になり**、各設定の要件を満たしていない場合はクライアントの接続が失われる可能性があります。

接続設定は、次のスクリーンショットに示すように、 **[ファイアウォールと仮想ネットワーク]** ブレードからアクセスできます。

 ![接続設定のスクリーンショット][1]


## <a name="deny-public-network-access"></a>パブリック ネットワーク アクセスの拒否
Azure portal で **[パブリック ネットワーク アクセスの拒否]** 設定が **[はい]** に設定されている場合、プライベート エンドポイント経由の接続のみが許可されます。 この設定を **[いいえ]** に設定すると、クライアントはプライベートまたはパブリック エンドポイントを使用して接続できます。

**[パブリック ネットワーク アクセスの拒否]** を **[はい]** に設定すると、パブリック エンドポイントを使用するクライアントからのログイン試行は失敗し、次のエラーが表示されます。

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>PowerShell を使用してパブリック ネットワーク アクセスを変更する
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 次のスクリプトは [Azure PowerShell](/powershell/azure/install-az-ps) モジュールを必要とします。

次の PowerShell スクリプトは、**パブリック ネットワーク アクセス** プロパティを論理サーバー レベルで `Get` および `Set` する方法を示しています。

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>CLI を使用してパブリック ネットワーク アクセスを変更する
> [!IMPORTANT]
> このセクションのすべてのスクリプトでは [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) が必要です。

### <a name="azure-cli-in-a-bash-shell"></a>bash シェルでの Azure CLI
次の CLI スクリプトは、bash シェルでの**パブリック ネットワーク アクセス**の変更方法を示しています。

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>接続ポリシー
クライアントが Azure SQL Server に接続する方法は、[接続ポリシー](sql-database-connectivity-architecture.md#connection-policy)によって決まります。 

## <a name="change-connection-policy-via-powershell"></a>PowerShell を使用して接続ポリシーを変更する
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 次のスクリプトは [Azure PowerShell](/powershell/azure/install-az-ps) モジュールを必要とします。

次の PowerShell スクリプトは、PowerShell を使用して接続ポリシーを変更する方法を示しています。

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>Azure CLI を使用して接続ポリシーを変更する
> [!IMPORTANT]
> このセクションのすべてのスクリプトでは [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) が必要です。

### <a name="azure-cli-in-a-bash-shell"></a>bash シェルでの Azure CLI
次の CLI スクリプトは、bash シェルでの接続ポリシーの変更方法を示しています。 

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Windows のコマンド プロンプトからの Azure CLI
次の CLI スクリプトは、(Azure CLI がインストールされている場合に) Windows コマンド プロンプトから接続ポリシーを変更する方法を示しています。

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>次のステップ
- Azure SQL Database で接続がどのように機能するかに関する概要については、「[Azure SQL の接続アーキテクチャ](sql-database-connectivity-architecture.md)」を参照してください
- Azure SQL Database サーバーの Azure SQL Database 接続ポリシーの変更方法については、「[conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)」を参照してください。

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
