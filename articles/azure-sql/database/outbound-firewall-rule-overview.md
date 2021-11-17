---
title: 送信ファイアウォール規則 (プレビュー)
description: 送信ファイアウォール規則の機能の概要。
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
ms.reviewer: vanto
ms.date: 11/10/2021
ms.openlocfilehash: d69536d8ed72dbd3793dd2481897d4534dbc2fdd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349864"
---
# <a name="outbound-firewall-rules-for-azure-sql-database-and-azure-synapse-analytics-preview"></a>Azure SQL Database と Azure Synapse Analytics の送信ファイアウォール規則 (プレビュー)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa-formerly-sqldw.md)] 

送信ファイアウォール規則により、Azure SQL 論理サーバーから、顧客が定義した Azure Storage アカウントおよび Azure SQL 論理サーバーの一覧へのネットワーク トラフィックが制限されます。 この一覧にないストレージ アカウントまたは SQL Database にアクセスしようとすると、拒否されます。 この機能をサポートする Azure SQL Database 機能は次のとおりです。

- [監査](auditing-overview.md)
- [脆弱性評価](sql-vulnerability-assessment.md)
- [I/E サービス](database-import-export-azure-services-off.md)
- OPENROWSET
- 一括挿入
- [エラスティック クエリ](elastic-query-overview.md)

> [!IMPORTANT]
> この記事は、Azure SQL Database と Azure Synapse Analytics の[専用 SQL プール (以前の SQL DW)](../../synapse-analytics\sql-data-warehouse\sql-data-warehouse-overview-what-is.md) の両方に適用されます。 これらの設定は、このサーバーに関連するすべての SQL Database と専用 SQL プール (以前の SQL DW) データベースに適用されます。 単純にするために、"データベース" という言葉で Azure SQL Database と Azure Synapse Analytics の両方のデータベースを表すことにします。 同様に、"サーバー" という言葉は、Azure SQL Database と Azure Synapse Analytics の専用 SQL プール (以前の SQL DW) をホストする[論理 SQL サーバー](logical-servers.md)を表しています。 この記事は、Azure SQL Managed Instance または Azure Synapse Analytics ワークスペースの専用 SQL プールには適用 "*されません*"。

## <a name="set-outbound-firewall-rules-in-the-azure-portal"></a>Azure portal で送信ファイアウォール規則を設定する

1. Azure SQL Database の **[ファイアウォールと仮想ネットワーク]** ブレードにある **[Outbound networking]\(送信ネットワーク\)** セクションに移動し、 **[Configure outbound networking restrictions]\(送信ネットワークの制限の構成\)** を選択します。

   ![[Outbound networking]\(送信ネットワーク\) セクションのスクリーンショット][1]  

   これにより、右側に次のブレードが表示されます。

   ![何も選択されていない送信ネットワーク ブレードのスクリーンショット][2]  

1. **[Restrict outbound networking]\(送信ネットワークの制限\)** というチェック ボックスをオンにし、 **[Add domain]\(ドメインの追加\)** ボタンを使用して Storage アカウント (または SQL Database) の FQDN を追加します。

   ![FQDN の追加方法を示す送信ネットワーク ブレードのスクリーンショット][3]  

1. 完了すると、以下のような画面が表示されます。 **[OK]** を選択して、これらの設定を適用します。

   ![FQDN を追加した後の送信ネットワーク ブレードのスクリーンショット][4]  

## <a name="set-outbound-firewall-rules-using-powershell"></a>PowerShell を使用して送信ファイアウォール規則を設定する

> [!IMPORTANT]
> Azure SQL Database では、PowerShell Azure Resource Manager モジュールが引き続きサポートされていますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 次のスクリプトは [Azure PowerShell](/powershell/azure/install-az-ps) モジュールを必要とします。

次の PowerShell スクリプトは、(**RestrictOutboundNetworkAccess** プロパティを使用して) 送信ネットワーク設定を変更する方法を示しています。

```powershell
# Get current settings for Outbound Networking
(Get-AzSqlServer -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName>).RestrictOutboundNetworkAccess

# Update setting for Outbound Networking
$SecureString = ConvertTo-SecureString "<ServerAdminPassword>" -AsPlainText -Force

Set-AzSqlServer -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -SqlAdministratorPassword $SecureString  -RestrictOutboundNetworkAccess "Enabled"
```

これらの PowerShell コマンドレットを使用して、送信ファイアウォール規則を構成します

```powershell
# List all Outbound Firewall Rules
Get-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName>

# Add an Outbound Firewall Rule
New-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN testOBFR1

# List a specific Outbound Firewall Rule
Get-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN <StorageAccountFQDN>

#Delete an Outbound Firewall Rule
Remove-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN <StorageAccountFQDN>
```

## <a name="set-outbound-firewall-rules-using-the-azure-cli"></a>Azure CLI を使用して送信ファイアウォール規則を設定する

> [!IMPORTANT]
> このセクションのすべてのスクリプトには [Azure CLI](/cli/azure/install-azure-cli) が必要です。

### <a name="azure-cli-in-a-bash-shell"></a>bash シェルでの Azure CLI

次の CLI スクリプトは、bash シェルで (**RestrictOutboundNetworkAccess** プロパティを使用して) 送信ネットワーク設定を変更する方法を示しています。

```azurecli-interactive
# Get current setting for Outbound Networking 
az sql server show -n sql-server-name -g sql-server-group --query "RestrictOutboundNetworkAccess"

# Update setting for Outbound Networking
az sql server update -n sql-server-name -g sql-server-group --set RestrictOutboundNetworkAccess="Enabled"
```

これらの CLI コマンドを使用して、送信ファイアウォール規則を構成します

```azurecli-interactive
# List a server's outbound firewall rules.
az sql server outbound-firewall-rule list -g sql-server-group -s sql-server-name

# Create a new outbound firewall rule
az sql server outbound-firewall-rule create -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN

# Show the details for an outbound firewall rule.
az sql server outbound-firewall-rule show -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN

# Delete the outbound firewall rule.
az sql server outbound-firewall-rule delete -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN
```

## <a name="next-steps"></a>次のステップ

- Azure SQL Database のセキュリティの概要については、「[データベースの保護](security-overview.md)」を参照してください。
- Azure SQL Database 接続の概要については、「[Azure SQL の接続アーキテクチャ](connectivity-architecture.md)」を参照してください

<!--Image references-->
[1]: media/outbound-firewall-rules/Step1.jpg
[2]: media/outbound-firewall-rules/Step2.jpg
[3]: media/outbound-firewall-rules/Step3.jpg
[4]: media/outbound-firewall-rules/Step4.jpg
