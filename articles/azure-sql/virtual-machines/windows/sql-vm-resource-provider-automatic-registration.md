---
title: SQL VM リソース プロバイダーへの自動登録
description: Azure portal を使用して自動登録機能を有効にし、既存および今後のすべての SQL Server VM を SQL VM リソース プロバイダーに自動的に登録する方法について説明します。
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: b986832e5febbb2a0f88b65213f9acf0dd4c5ab5
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996897"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>SQL VM リソース プロバイダーへの自動登録
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure portal で自動登録機能を有効にすると、Azure VM 上の既存および今後のすべての SQL Server は軽量モードの SQL VM リソース プロバイダーに自動的に登録されます。

この記事では、自動登録機能を有効にする方法について説明します。 また、SQL VM リソース プロバイダーに [1 つの VM を登録する](sql-vm-resource-provider-register.md)ことや、[複数の VM を一括で登録する](sql-vm-resource-provider-bulk-register.md)こともできます。 

## <a name="overview"></a>概要

[SQL VM リソース プロバイダー](sql-vm-resource-provider-register.md#overview)を使用すると、Azure portal から SQL Server VM を管理できます。 さらに、リソース プロバイダーを使用すると、[自動パッチ適用](automated-patching.md)、[自動バックアップ](automated-backup.md)、監視および管理機能などの堅牢な機能セットが有効になります。 また、[ライセンス](licensing-model-azure-hybrid-benefit-ahb-change.md)や[エディション](change-sql-server-edition.md)の柔軟性も実現されます。 これらの機能は、以前は Azure Marketplace からデプロイされた SQL Server VM イメージでしか利用できませんでした。 

自動登録機能を使用すると、Azure サブスクリプション内の既存および今後のすべての SQL Server VM を SQL VM リソース プロバイダーに自動的に登録できます。 これは、既存の SQL Server VM のみに焦点を当てた手動登録とは異なります。 

自動登録により、SQL Server VM は軽量モードで登録されます。 完全な機能セットを使用するには、[完全管理モードに手動でアップグレードする](sql-vm-resource-provider-register.md#upgrade-to-full)必要があります。 

## <a name="prerequisites"></a>前提条件

SQL Server VM をリソース プロバイダーに登録するには、次のものが必要になります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- パブリック クラウドまたは Azure Government クラウドにデプロイされた Azure Resource Model の [Windows 仮想マシン](../../../virtual-machines/windows/quick-create-portal.md)と [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads)。 


## <a name="enable"></a>有効にする

Azure portal で SQL Server VM の自動登録を有効にするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. [ **[SQL 仮想マシン]** ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) リソース ページに移動します。 
1. **[Automatic SQL Server VM registration]\(自動 SQL Server VM 登録\)** を選択して **[Automatic registration]\(自動登録\)** ページを開きます。 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="[Automatic SQL Server VM registration]\(自動 SQL Server VM 登録\) を選択して [Automatic registration]\(自動登録\) ページを開きます":::

1. ドロップダウンから使用するサブスクリプションを選択します。 
1. 条項を読み、同意する場合は **[同意する]** を選択します。 
1. **[登録]** を選択すると、この機能が有効になり、既存および今後のすべての SQL Server VM が SQL VM リソース プロバイダーに自動的に登録されるようになります。 これにより、どの VM 上の SQL Server サービスも再起動されません。 

## <a name="disable"></a>無効

自動登録機能を無効にするには、[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/install-az-ps) を使用します。 自動登録機能が無効になっている場合、サブスクリプションに追加された SQL Server VM は、SQL VM リソース プロバイダーに手動で登録する必要があります。 これにより、既に登録されている既存の SQL Server VM の登録が解除されることはありません。



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して自動登録を無効にするには、次のコマンドを実行します。 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell を使用して自動登録を無効にするには、次のコマンドを実行します。 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>複数のサブスクリプションに対して有効にする

PowerShell を使用すると、複数の Azure サブスクリプションの自動登録機能を有効にすることができます。 

これを行うには、次の手順に従います。

1. [このスクリプト](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1)を `EnableBySubscription.ps1` などの `.ps1` ファイルに保存します。 
1. 管理コマンド プロンプトまたは PowerShell ウィンドウを使用して、スクリプトを保存した場所に移動します。 
1. Azure に接続します (`az login`)。
1. スクリプトを実行し、SubscriptionIds を次のようなパラメーターとして渡します。   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   次に例を示します。 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

登録の失敗エラーは、`.ps1` スクリプトの保存と実行に使用したものと同じディレクトリ内にある `RegistrationErrors.csv` に格納されます。 

## <a name="next-steps"></a>次のステップ

管理モードを[完全](sql-vm-resource-provider-register.md#upgrade-to-full)にアップグレードして、SQL VM リソース プロバイダーによって提供される完全な機能セットを使用します。 
