---
title: Azure SQL Database と Data Warehouse の接続の設定
description: このドキュメントでは、TLS バージョンの選択と、Azure SQL のプロキシとリダイレクトの設定の比較について説明します
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: ef846b23bf6c9da2b7dd64927eac7cc1a1704dae
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684938"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL の接続の設定
> [!NOTE]
> この記事は、論理サーバー上に作成される Azure SQL Database データベースと SQL Data Warehouse データベースの両方に使用される、Azure の論理 SQL サーバーに適用されます。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。

> [!IMPORTANT]
> この記事は、**Azure SQL Database Managed Instance** には適用*されません*。

この記事では、Azure SQL Database への接続をサーバー レベルで制御する設定について説明します。 これらの設定は、サーバーに関連する**すべての** SQL Database および SQL Data Warehouse データベースに適用されます。

> [!NOTE]
> これらの設定を適用すると、それらは**直ちに有効になり**、各設定の要件を満たしていない場合はクライアントの接続が失われる可能性があります。

接続設定は、次のスクリーンショットに示すように、 **[ファイアウォールと仮想ネットワーク]** ブレードからアクセスできます。

 ![接続設定のスクリーンショット][1]


## <a name="deny-public-network-access"></a>パブリック ネットワーク アクセスの拒否

お客様は、[ネットワーク アクセスの概要](sql-database-networkaccess-overview.md)に記載されているように、パブリック エンドポイント (IP ベースのファイアウォール規則、VNET ベースのファイアウォール規則) またはプライベート エンドポイント (Private Link を使用) を使用して SQL Database に接続できます。 

**[Deny public network access]\(パブリック ネットワーク アクセスを拒否する\)** 設定を **[はい]** に設定すると、プライベート エンドポイント経由の接続のみが許可され、パブリック エンドポイント経由の接続はすべて拒否され、次のようなエラーメッセージが表示されます。  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
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

## <a name="minimal-tls-version"></a>最小 TLS バージョン 

最小 [トランスポート層セキュリティ (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) バージョン設定を使用すると、ユーザーは Azure SQL Database で使用される TLS のバージョンを制御できます。

現時点では、TLS 1.0、1.1、1.2 がサポートされています。 最小 TLS バージョンを設定すると、以降の新しい TLS バージョンがサポートされるようになります。 たとえば、1.1 よりも大きい TLS バージョンを選択する ことは、TLS 1.1 および 1.2 との接続のみが受け入れられ、TLS 1.0 は拒否されることを意味します。 テスト後、アプリケーションでこれがサポートされていることを確認するために、最小 TLS バージョンを 1.2 に設定することをお勧めします。これは、以前のバージョンの脆弱性に対する修正プログラムが含まれており、Azure SQL Database でサポートされる TLS の最高バージョンであるためです。

以前のバージョンの TLS に依存するアプリケーションを使用しているお客様には、アプリケーションの要件に従って最小 TLS バージョンを設定することをお勧めします。 暗号化されていない接続を使用して接続するアプリケーションに依存しているお客様は、最少 TLS バージョンを設定しないことをお勧めします。 

詳細については、「[SQL Database の接続に関する TLS の考慮事項](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity)」を参照してください。

最小 TLS バージョンを設定すると、サーバーの最小 TLS バージョンより低い TLS バージョンを使用しているクライアントからのログイン試行は、次のエラーで失敗します。

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>PowerShell を使用して最小 TLS バージョンを設定する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 次のスクリプトは [Azure PowerShell](/powershell/azure/install-az-ps) モジュールを必要とします。

次の PowerShell スクリプトは、**最少 TLS バージョン** プロパティを論理サーバー レベルで `Get` および `Set` する方法を示しています。

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Azure CLI 経由で最小 TLS バージョンを設定する

> [!IMPORTANT]
> このセクションのすべてのスクリプトでは [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) が必要です。

### <a name="azure-cli-in-a-bash-shell"></a>bash シェルでの Azure CLI

次の CLI スクリプトは、bash シェルでの**最少 TLS バージョン**の変更方法を示しています。

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
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
