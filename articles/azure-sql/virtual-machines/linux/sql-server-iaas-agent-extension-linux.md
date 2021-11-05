---
title: Linux 用の SQL Server IaaS エージェント拡張機能
description: この記事では、SQL Server IaaS エージェント拡張機能を使用して、SQL Server on Linux Azure VM の管理固有の管理タスクを自動化する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: adbadram
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.openlocfilehash: 98e448358d31fda34a8bb84024be6daf157d0f78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092637"
---
# <a name="sql-server-iaas-agent-extension-for-linux"></a>Linux 用の SQL Server IaaS エージェント拡張機能
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-iaas-agent-extension-automate-management.md)
> * [Linux](sql-server-iaas-agent-extension-linux.md)

SQL Server IaaS エージェント拡張機能 (SQLIaaSExtension) は、管理タスクを自動化するために SQL Server on Linux Azure Virtual Machines (VM) で実行されます。 

この記事では、この拡張機能の概要について説明します。 詳細については、[拡張機能の登録](sql-iaas-agent-extension-register-vm-linux.md)に関する記事を参照してください。 


## <a name="overview"></a>概要

SQL Server IaaS エージェント拡張機能を使用すると、Azure portal との統合が可能になり、SQL Server on Linux Azure VM の次の利点が利用できるようになります。 

- **コンプライアンス**: この拡張機能では、製品の条件に指定されているとおりに Azure ハイブリッド特典が有効になっていることを Microsoft に通知するという要件を満たすための簡単な方法が提供されます。 このプロセスにより、リソースごとにライセンス登録フォームを管理する必要がなくなります。  

- **ライセンス管理の簡素化**: 拡張機能によって SQL Server ライセンス管理が簡略化され、Azure portal、Azure PowerShell、または Azure CLI を使用して Azure ハイブリッド特典が有効になっている SQL Server VM をすばやく識別できます。 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---

- **無料**: 拡張機能に関連する追加コストはありません。 



## <a name="installation"></a>インストール

SQL Server IaaS エージェント拡張機能を使用して SQL Server VM を [登録](sql-iaas-agent-extension-register-vm-linux.md)して、お使いのサブスクリプション内に **SQL 仮想マシン** リソースを作成します。これは、仮想マシンリソースとは別のリソースです。 拡張機能から SQL Server VM を登録解除すると、サブスクリプションから **SQL 仮想マシン** の "_リソース_" は削除されますが、実際の仮想マシンは削除されません。

現在 Linux 用の SQL Server IaaS エージェント拡張機能は、軽量モードでのみ使用できます。 


## <a name="verify-extension-status"></a>拡張機能の状態を確認する

Azure portal または Azure PowerShell を使用して、拡張機能の状態を確認します。 

### <a name="azure-portal"></a>Azure portal

Azure portal を使用して拡張機能がインストールされていることを確認します。 

Azure portal で **[仮想マシン]** リソースに移動します ( *[SQL 仮想マシン]* リソースではなく、お使いの VM のリソースです)。 **[設定]** で **[拡張機能]** を選択します。 次の例のように、**SqlIaasExtension** 拡張機能が一覧に表示されます。 

![Azure portal で SQL Server IaaS エージェント拡張機能 (SqlIaaSExtension) の状態を確認する](../windows/media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)




### <a name="azure-powershell"></a>Azure PowerShell

**Get-AzVMSqlServerExtension** Azure PowerShell コマンドレットを使用することもできます。

```powershell-interactive
  Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
```

前のコマンドでは、エージェントがインストールされていることが確認され、全般的な状態情報が提供されます。 次のコマンドを使用して、自動バックアップと自動修正に関する特定の状態情報を取得できます。

```powershell-interactive
 $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
 $sqlext.AutoPatchingSettings
 $sqlext.AutoBackupSettings
```

## <a name="limitations"></a>制限事項

Linux 用の SQL IaaS エージェント拡張機能には、次の制限があります。 

- Ubuntu Linux オペレーティング システムで実行されている SQL Server VM のみがサポートされています。 その他の Linux ディストリビューションは現在サポートされていません。
- Ubuntu Linux Pro で実行される SQL Server VM はサポートされていません。
- 一般化されたイメージで実行される SQL Server VM はサポートされていません。
- Azure Resource Manager を介してデプロイされた SQL Server VM のみがサポートされます。 クラシック モデルを介してデプロイされた SQL Server VM はサポートされません。 
- 1 つのインスタンスのみの SQL Server。 複数のインスタンスはサポートされていません。 

## <a name="privacy-statement"></a><a id="in-region-data-residency"></a> プライバシーに関する声明

Azure VM 上の SQL Server と SQL IaaS 拡張機能を使用する場合は、次のプライバシーに関する声明を考慮してください。 

- **データ収集**: SQL IaaS エージェント拡張機能を使用すると、Azure Virtual Machines 上の SQL Server を使用するお客様に追加のメリットを提供するという特定の目的のために、データが収集されます。 Microsoft は、お客様の事前の同意なく、**ライセンスの監査にこのデータを使用することはありません**。 詳細については、「[SQL Server のプライバシーの補足情報](/sql/sql-server/sql-server-privacy#non-personal-data)」を参照してください。

- **リージョン内のデータの所在**: Azure VM 上の SQL Server と SQL IaaS Agent 拡張機能によって、VM がデプロイされているリージョン外に顧客データが移動されたり、保存されたりすることはありません。 


## <a name="next-steps"></a>次のステップ

Azure Virtual Machines で SQL Server を実行する方法の詳細については、[Azure Linux Virtual Machines における SQL Server の概要](sql-server-on-linux-vm-what-is-iaas-overview.md)に関する記事をご覧ください。

詳細については、[よく寄せられる質問](frequently-asked-questions-faq.yml)を参照してください。
