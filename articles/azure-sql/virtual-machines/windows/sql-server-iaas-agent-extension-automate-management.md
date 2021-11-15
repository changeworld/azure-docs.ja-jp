---
title: SQL Server IaaS Agent 拡張機能とは (Windows)
description: この記事では、SQL Server IaaS Agent 拡張機能を使用して、Windows Azure VM 上の SQL Server の管理固有の管理タスクを自動化する方法について説明します。 この中には、自動バックアップ、自動修正、Azure Key Vault の統合、ライセンス管理、ストレージ構成、すべての SQL Server VM インスタンスの中央管理といった機能が含まれます。
services: virtual-machines-windows
documentationcenter: ''
author: adbadram
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/26/2021
ms.author: adbadram
ms.reviewer: mathoma
ms.custom: seo-lt-2019, ignite-fall-2021
ms.openlocfilehash: 35ea46e4ce2b7c4ebbb6fdfa24bbe1d60e679bb7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345702"
---
# <a name="automate-management-with-the-windows-sql-server-iaas-agent-extension"></a>Windows SQL Server IaaS Agent 拡張機能を使用して管理を自動化する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-iaas-agent-extension-automate-management.md)
> * [Linux](../linux/sql-server-iaas-agent-extension-linux.md)



SQL Server IaaS Agent 拡張機能 (SQLIaaSExtension) は、管理タスクを自動化するために Windows Azure Virtual Machines (VM) 上の SQL Server で実行されます。 

この記事では、この拡張機能の概要について説明します。 SQL Server IaaS 拡張機能を Azure VM 上の SQL Server にインストールする方法については、[自動インストール](sql-agent-extension-automatic-registration-all-vms.md)、 [単一の VM の登録](sql-agent-extension-manually-register-single-vm.md)、または [VM の一括登録](sql-agent-extension-manually-register-vms-bulk.md) に関する記事を参照してください。 

> [!NOTE]
> 2021 年 9 月以降は、SQL IaaS 拡張機能をフル モードで登録しても、SQL Server サービスを再起動する必要はありません。 

## <a name="overview"></a>概要

SQL Server IaaS Agent 拡張機能を使用すると、Azure portal との統合が可能になり、管理モードによっては、Azure VM 上の SQL Server のいくつかの機能の利点が明らかになります。 

- **機能面の利点**: この拡張機能では、ポータル管理、ライセンスの柔軟性、自動バックアップ、自動修正といった、自動化機能のさまざまな利点を活用できます。 詳細については、この記事の後半にある「[機能面の利点](#feature-benefits)」を参照してください。 

- **コンプライアンス**: この拡張機能では、製品の条件に指定されているとおりに Azure ハイブリッド特典が有効になっていることを Microsoft に通知するという要件を満たすための簡単な方法が提供されます。 このプロセスにより、リソースごとにライセンス登録フォームを管理する必要がなくなります。  

- **Free**:3 つすべての管理モードの拡張機能は、完全に無料です。 拡張機能または管理モードの変更に関連する追加のコストは発生しません。 

- **簡略化されたライセンス管理**:この拡張機能では、SQL Server ライセンスの管理が簡略化されるほか、[Azure portal](manage-sql-vm-portal.md)、PowerShell または Azure CLI を使用して、Azure ハイブリッド特典が有効になった SQL Server VM をすばやく識別することができます。 

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $ az sql vm list --query "[?sqlServerLicenseType=='AHUB']"
   ```
   ---



## <a name="feature-benefits"></a>機能面の利点 

SQL Server IaaS Agent 拡張機能には、SQL Server VM を管理するための機能面の利点が複数あります。 

次の表に、これらの利点の詳細を示します。 


| 機能 | 説明 |
| --- | --- |
| **ポータル管理** | [ポータルで管理することができます](manage-sql-vm-portal.md)。このため、すべての SQL Server VM を 1 か所に表示でき、ポータルから SQL 固有の機能を直接有効または無効にすることができます。 <br/> 管理モード: Lightweight および完全|  
| **自動バックアップ** |VM 上の SQL Server の既定のインスタンスまたは[適切にインストールされた](./frequently-asked-questions-faq.yml)名前付きインスタンスについて、すべてのデータベースのバックアップを自動的にスケジュールします。 詳細については、[Azure 仮想マシンでの SQL Server の自動バックアップ (Resource Manager)](automated-backup-sql-2014.md) に関する記事を参照してください。 <br/> 管理モード: [完全]|
| **自動修正** |VM で Windows と SQL Server の重要なセキュリティ更新プログラムを実行できるメンテナンス期間を構成します。こうすることで、ワークロードのピーク時に更新が実行されるのを回避できます。 詳細については、「[Azure Virtual Machines での SQL Server の自動修正 (Resource Manager)](automated-patching.md)」をご覧ください。 <br/> 管理モード: [完全]|
| **Azure Key Vault の統合** |SQL Server VM に Azure Key Vault を自動的にインストールして構成できます。 詳細については、「[Azure Virtual Machines 上の SQL Server 向け Azure Key Vault 統合の構成 (Resource Manager)](azure-key-vault-integration-configure.md)」をご覧ください。 <br/> 管理モード: [完全]|
| **ポータルでディスク使用率を表示する** | Azure portal 内の SQL データ ファイルのディスク使用率をグラフィカルに表示できます。  <br/> 管理モード: [完全] | 
| **柔軟なライセンス** | ライセンス持ち込み (「Azure ハイブリッド特典」とも呼ばれます) から従量課金制のライセンス モデルに、またはその逆に[シームレスに移行](licensing-model-azure-hybrid-benefit-ahb-change.md)することで、コストを節約できます。 <br/> 管理モード: Lightweight および完全| 
| **柔軟なバージョン/エディション** | SQL Server の[バージョン](change-sql-server-version.md)または[エディション](change-sql-server-edition.md)を変更する場合は、SQL Server VM 全体を再デプロイすることなく、Azure portal 内のメタデータを更新できます。  <br/> 管理モード: Lightweight および完全| 
| **Defender for Cloud ポータル統合** | [Microsoft Defender for SQL](../../../security-center/defender-for-sql-usage.md) を有効にすると、Azure portal の [SQL 仮想マシン](manage-sql-vm-portal.md) リソースで Defender for Cloud 推奨事項を直接閲覧できます。 詳細については、[セキュリティのベスト プラクティス](security-considerations-best-practices.md)を参照してください。  <br/> 管理モード: Lightweight および完全|
| **SQL評価 (プレビュー)** | 構成のベストプラクティスを使用して SQL Server vm の正常性を評価できます。 詳細については、「[SQL Assessment API](sql-assessment-for-sql-vm.md)」を参照してください。  <br/> 管理モード: [完全]| 


## <a name="management-modes"></a>管理モード

次の 3 つの管理モードによる SQL IaaS 拡張機能の登録を選択できます。 

- **軽量** モードでは、拡張機能バイナリが VM にコピーされますが、エージェントはインストールされません。 軽量モードでは、SQL Server のライセンスの種類とエディションの変更 "_のみ_" がサポートされ、ポータルの管理が制限されます。 このオプションは、複数のインスタンスがあるか、フェールオーバー クラスター インスタンス (FCI) に参加している SQL Server VM に使用します。 簡易モードは、[自動登録](sql-agent-extension-automatic-registration-all-vms.md)機能を使用する場合、または手動登録時に管理の種類が指定されていない場合の既定の管理モードです。 軽量モードを使用する場合、メモリまたは CPU への影響はなく、また関連コストはありません。 

- **フル** モードでは、SQL IaaS Agent が VM にインストールされてすべての機能が提供されます。 単一のインスタンスがある SQL Server VM を管理する場合に使用します。 フル モードでは、メモリと CPU への影響を最低限に抑えた 2 つの Windows サービスをインストールします - これらはタスク マネージャーでモニターできます。 完全管理モードの使用に関連するコストはありません。 システム管理者のアクセス許可が必要です。 2021 年 9 月以降は、SQL Server VM をフル管理モードで登録したときでも、SQL Server サービスを再起動する必要はありません。 

- **NoAgent** モードは Windows Server 2008 にインストールされた SQL Server 2008 および SQL Server 2008 R2 の専用モードです。 NoAgent モードを使用する場合、メモリまたは CPU への影響はありません。 NoAgent 管理モードの使用に関連するコストはありません。SQL Server は再起動されず、エージェントは VM にインストールされません。 

Azure PowerShell を次のように使用して、SQL Server IaaS Agent の現在のモードを確認できます。 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```


## <a name="installation"></a>インストール

SQL Server VM を SQL Server IaaS Agent 拡張機能に登録し、仮想マシン リソースとは "_別_ の" リソースである、[**SQL 仮想マシン** "_リソース_"](manage-sql-vm-portal.md) をサブスクリプション内に作成します。 拡張機能から SQL Server VM を登録解除すると、サブスクリプションから **SQL 仮想マシン** の "_リソース_" は削除されますが、実際の仮想マシンは削除されません。

Azure portal を介して SQL Server VM の Azure Marketplace イメージをデプロイすると、その SQL Server VM が自動的に拡張機能にフル モードで登録されます。 ただし、Azure 仮想マシンに SQL Server を自分でインストールすること、またはカスタム VHD から Azure 仮想マシンをプロビジョニングすることを選択する場合は、機能面の利点を活用できるようにするために、SQL Server VM を SQL IaaS Agent 拡張機能に登録する必要があります。 

軽量モードで拡張機能を登録すると、バイナリはコピーされますが、エージェントは VM にインストールされません。 拡張機能がフル管理モードでインストールされると、エージェントは VM にインストールされます。 

拡張機能に登録する方法は 3 つあります。 
- [サブスクリプション内の現在の VM および今後の VM をすべて自動登録する場合](sql-agent-extension-automatic-registration-all-vms.md)
- [単一の VM を手動で](sql-agent-extension-manually-register-single-vm.md)
- [複数の VM を一括で手動で](sql-agent-extension-manually-register-vms-bulk.md)

既定では、SQL Server 2016 以降がインストールされている Azure VM は、[CEIP サービス](/sql/sql-server/usage-and-diagnostic-data-configuration-for-sql-server)によって検出されると自動的に SQL IaaS Agent 拡張機能に登録されます。  詳細については、「[SQL Server のプライバシーの補足情報](/sql/sql-server/sql-server-privacy#non-personal-data)」を参照してください。


### <a name="named-instance-support"></a>名前付きインスタンスのサポート

SQL Server IaaS Agent 拡張機能は、それが仮想マシンで使用できる唯一の SQL Server インスタンスである場合に、SQL Server の名前付きインスタンスを使用して動作します。 VM に SQL Server の名前付きインスタンスが複数あり、既定のインスタンスがない場合、SQL IaaS 拡張機能では軽量モードで登録され、最上位のエディションのインスタンス、またはすべてのインスタンスのエディションが同じである場合は最初のインスタンスが選択されます。 

SQL Server の名前付きインスタンスを使用するには、Azure 仮想マシンをデプロイし、1 つの名前付き SQL Server インスタンスをそれにインストールし、その後、[SQL IaaS 拡張機能](sql-agent-extension-manually-register-single-vm.md)にそれを登録します。

または、Azure Marketplace SQL Server イメージで名前付きインスタンスを使用するには、次の手順を実行します。 

   1. Azure Marketplace から SQL Server VM をデプロイします。 
   1. SQL IaaS Agent 拡張機能から SQL Server VM の[登録を解除](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)します。 
   1. SQL Server VM 内で SQL Server を完全にアンインストールします。
   1. SQL Server VM 内の名前付きインスタンスで SQL Server をインストールします。 
   1. [SQL IaaS Agent 拡張機能に VM を登録します](sql-agent-extension-manually-register-single-vm.md#full-mode)。 

## <a name="verify-status-of-extension"></a>拡張機能の状態を確認する

Azure portal または Azure PowerShell を使用して、拡張機能の状態を確認します。 

### <a name="azure-portal"></a>Azure portal

Azure portal に拡張機能がインストールされていることを確認します。 

Azure portal で **[仮想マシン]** リソースに移動します ( *[SQL 仮想マシン]* リソースではなく、お使いの VM のリソースです)。 **[設定]** で **[拡張機能]** を選択します。  次の例のように、**SqlIaasExtension** 拡張機能が一覧に表示されます。 

![Azure portal での SQL Server IaaS Agent 拡張機能の状態](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


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

SQL IaaS Agent 拡張機能では、以下のみがサポートされます。 

- Azure Resource Manager を介してデプロイされた SQL Server VM。 クラシック モデルを介してデプロイされた SQL Server VM はサポートされません。 
- パブリック クラウドまたは Azure Government クラウドにデプロイされた SQL Server VM。 その他のプライベート クラウドや政府機関向けクラウドにデプロイされている場合はサポートされません。 
- 軽量モードのフェールオーバー クラスター インスタンス (FCI)。 
- 軽量モードの 1 つの VM 上に複数のインスタンスがある名前付きインスタンス。 


## <a name="privacy-statement"></a><a id="in-region-data-residency"></a> プライバシーに関する声明

Azure VM 上の SQL Server と SQL IaaS 拡張機能を使用する場合は、次のプライバシーに関する声明を考慮してください。 

- **データ収集**: SQL IaaS Agent 拡張機能を使用すると、Azure Virtual Machines 上の SQL Server を使用するお客様に追加のメリットを提供するという明確な目的のために、データが収集されます。 Microsoft は、お客様の事前の同意なく、**ライセンスの監査にこのデータを使用することはありません**。詳細については、「[SQL Server のプライバシーの補足情報](/sql/sql-server/sql-server-privacy#non-personal-data)」を参照してください。

- **リージョン内のデータの所在**: Azure VM 上の SQL Server と SQL IaaS Agent 拡張機能によって、VM がデプロイされているリージョン外に顧客データが移動されたり、保存されたりすることはありません。


## <a name="next-steps"></a>次のステップ

SQL Server IaaS 拡張機能を Azure VM 上の SQL Server にインストールする方法については、[自動インストール](sql-agent-extension-automatic-registration-all-vms.md)、 [単一の VM の登録](sql-agent-extension-manually-register-single-vm.md)、または [VM の一括登録](sql-agent-extension-manually-register-vms-bulk.md) に関する記事を参照してください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「[Azure Virtual Machines における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)」を参照してください。

詳細については、[よく寄せられる質問](frequently-asked-questions-faq.yml)を参照してください。
