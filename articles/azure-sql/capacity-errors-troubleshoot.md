---
title: Azure SQL リソースの容量エラーを解決する
description: Azure SQL Database または Azure SQL Managed Instance のリソースをデプロイまたはスケーリングしようとするときに発生するおそれのある容量エラーを解決する方法について説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: deployment-configuration
ms.topic: how-to
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: mathoma
ms.date: 09/03/2021
ms.custom: references_regions
ms.openlocfilehash: 885be735055eaf65d67466694e65d6cf7fdf00da
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481531"
---
# <a name="resolve-capacity-errors-with-azure-sql-database-or-azure-sql-managed-instance"></a>Azure SQL Database または Azure SQL Managed Instance の容量エラーを解決する
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

この記事では、Azure SQL Database または Azure SQL Managed Instance のリソースをデプロイする際の容量エラーを解決する方法について説明します。 

## <a name="exceeded-quota"></a>超過クォータ 

Azure SQL リソースをデプロイしようとしたときに次のエラーのいずれかが発生した場合は、[クォータの増加を要求](database/quota-increase-request.md)してください。 

- `Server quota limit has been reached for this location. Please select a different location with lower server count.`
- `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx.`
- スケーリングの操作中に、次のエラーが表示される場合があります。    
  `Could not perform the operation because server would exceed the allowed Database Throughput Unit quota of xx. `. 

## <a name="subscription-access"></a>サブスクリプションへのアクセス

お客様のサブスクリプションが SQL リソース プロバイダー (RP) にまだ登録されていない場合、そのサブスクリプションには、選択したリージョンにサーバーを作成するためのアクセス権がないことがあります。  

次のエラーが表示された場合は、[ご使用のサブスクリプションを SQL RP に登録](#register-with-sql-rp)してください。
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`


## <a name="enable-region"></a>リージョンの有効化 

選択したリージョンが有効になっていない場合、ご使用のサブスクリプションには、そのリージョンにサーバーを作成するためのアクセス権がないことがあります。 これを解決するには、ご使用のサブスクリプションに対して[特定のリージョンを有効にするためのサポート リクエスト](database/quota-increase-request.md#region)を提出します。 

次のエラーが表示された場合は、特定のリージョンを有効にするためのサポート チケットを提出してください。 
- `Your subscription does not have access to create a server in the selected region.`
- `Provisioning is restricted in this region. Please choose a different region. For exceptions to this rule please open a support request with issue type of 'Service and subscription limits' `
- `Location 'region name' is not accepting creation of new Windows Azure SQL Database servers for the subscription 'subscription id' at this time`



## <a name="register-with-sql-rp"></a>SQL RP への登録

Azure SQL リソースをデプロイするには、ご使用のサブスクリプションを SQL リソース プロバイダー (RP) に登録します。 

サブスクリプションは、Azure portal、[Azure CLI](/cli/azure/install-azure-cli)、または [Azure PowerShell](/powershell/azure/install-az-ps) を使用して登録できます。 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でサブスクリプションを登録するには、次の手順を実行します。 

1. Azure portal を開き、 **[すべてのサービス]** に移動します。
1. **[サブスクリプション]** に移動し、目的のサブスクリプションを選択します。
1. **[サブスクリプション]** ページで、 **[設定]** の **[リソース プロバイダー]** を選択します。
1. フィルターに「**sql**」と入力し、SQL 関連の拡張機能を表示します。
1. 必要なアクションに応じて、**Microsoft.Sql** プロバイダーに対する **[登録]** 、 **[再登録]** 、または **[登録解除]** を選択します。

   ![プロバイダーの変更](./media/capacity-errors-troubleshoot/register-with-sql-rp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

[Azure CLI](/cli/azure/install-azure-cli) を使用してサブスクリプションを登録するには、次のコマンドレットを実行します。

```azurecli-interactive
# Register the SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMac 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[Azure PowerShell](/powershell/azure/install-az-ps) を使用してサブスクリプションを登録するには、次のコマンドレットを実行します。 

```powershell-interactive
# Register the SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.Sql

```

---

## <a name="additional-provisioning-issues"></a>プロビジョニングに関するその他の問題

プロビジョニングに関する問題が発生し続ける場合は、SQL Database のサポート トピックで **[リージョン]** のアクセス要求を作成し、Azure SQL Database または Azure SQL Managed Instance で使用したい DTU または仮想コア数を指定してください。 

## <a name="azure-program-regions"></a>Azure プログラムのリージョン 

Azure プログラム オファリング (Azure Pass、Imagine、Azure for Students、MPN、BizSpark、BizSpark Plus、Microsoft for Startups またはスポンサー プラン、Visual Studio サブスクリプションまたは MSDN) は、アクセスできるリージョンが限られています。 

お客様のサブスクリプションが Azure プログラム オファリングの一部で、次のリージョンのいずれかへのアクセスを要求する場合は、代わりに代替リージョンの使用をご検討ください。 

_オーストラリア中部、オーストラリア中部 2、オーストラリア南東部、ブラジル南東部、カナダ東部、中国東部、中国北部、中国北部 2、フランス南部、ドイツ北部、西日本、JIO インド中部、JIO インド西部、韓国南部、ノルウェー西部、南アフリカ西部、インド南部、スイス西部、アラブ首長国連邦中部、英国西部、US DoD 中部、US DoD 東部、US Gov アリゾナ、US Gov テキサス、米国中西部、インド西部。_ 

## <a name="next-steps"></a>次のステップ

要求を送信すると、そのレビューが行われます。 フォームに指定した情報に基づいた回答を受け取ります。

Azure の他の制限の詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md)」をご覧ください。
