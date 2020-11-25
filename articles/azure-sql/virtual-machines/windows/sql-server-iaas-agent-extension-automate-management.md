---
title: SQL Server IaaS Agent 拡張機能とは
description: この記事では、SQL Server IaaS Agent 拡張機能を使用して、Azure VM 上の SQL Server の管理固有の管理タスクを自動化する方法について説明します。 この中には、自動バックアップ、自動修正、Azure Key Vault の統合、ライセンス管理、ストレージ構成、すべての SQL Server VM インスタンスの中央管理といった機能が含まれます。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 572363f429cb828d44c9dd12ba2424930c94fefe
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553535"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS Agent 拡張機能を使用して管理を自動化する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS Agent 拡張機能 (SQLIaaSExtension) は、管理タスクを自動化するために Azure 仮想マシン (VM) 上の SQL Server で実行されます。 

この記事では、この拡張機能の概要について説明します。 SQL Server IaaS 拡張機能を Azure VM 上の SQL Server にインストールする方法については、[自動インストール](sql-agent-extension-automatic-registration-all-vms.md)、 [単一の VM の登録](sql-agent-extension-manually-register-single-vm.md)、または [VM の一括登録](sql-agent-extension-manually-register-vms-bulk.md) に関する記事を参照してください。 

## <a name="overview"></a>概要

SQL Server IaaS Agent 拡張機能を使用すると、Azure VM 上の SQL Server でさまざまな利点を活用できます。 

- **機能面の利点**: この拡張機能では、ポータル管理、ライセンスの柔軟性、自動バックアップ、自動修正といった、自動化機能のさまざまな利点を活用できます。 詳細については、この記事の後半にある「[機能面の利点](#feature-benefits)」を参照してください。 

- **コンプライアンス**:この拡張機能では、製品の条件に指定されているとおりに Azure ハイブリッド特典が有効になっていることを Microsoft に通知する要件を満たす簡単な方法が提供されます。 このプロセスにより、リソースごとにライセンス登録フォームを管理する必要がなくなります。  

- **Free**:3 つすべての管理モードの拡張機能は、完全に無料です。 拡張機能または管理モードの変更に関連する追加のコストは発生しません。 

- **簡略化されたライセンス管理**:この拡張機能では、SQL Server ライセンスの管理が簡略化されるほか、[Azure portal](manage-sql-vm-portal.md)、Azure CLI、または PowerShell を使用して、Azure ハイブリッド特典が有効になった SQL Server VM をすばやく識別することができます。 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---


> [!IMPORTANT]
> SQL IaaS Agent 拡張機能を使用すると、Azure 仮想マシン内で SQL Server を使用する際に、お客様に追加のメリットを提供するという明確な目的のためにデータが収集されます。 Microsoft は、お客様の事前の同意なく、ライセンスの監査にこのデータを使用することはありません。 詳細については、「[SQL Server のプライバシーの補足情報](/sql/sql-server/sql-server-privacy#non-personal-data)」を参照してください。


## <a name="feature-benefits"></a>機能面の利点 

SQL Server IaaS Agent 拡張機能には、SQL Server VM を管理するための機能面の利点が複数あります。 

次の表に、これらの利点の詳細を示します。 


| 機能 | 説明 |
| --- | --- |
| **ポータル管理** | [ポータルで管理することができます](manage-sql-vm-portal.md)。このため、すべての SQL Server VM を 1 か所に表示でき、ポータルから SQL 固有の機能を直接有効または無効にすることができます。 
| **自動バックアップ** |VM 上の SQL Server の既定のインスタンスまたは[適切にインストールされた](frequently-asked-questions-faq.md#administration)名前付きインスタンスについて、すべてのデータベースのバックアップを自動的にスケジュールします。 詳細については、[Azure 仮想マシンでの SQL Server の自動バックアップ (Resource Manager)](automated-backup-sql-2014.md) に関する記事を参照してください。 |
| **自動修正** |VM で Windows と SQL Server の重要なセキュリティ更新プログラムを実行できるメンテナンス期間を構成します。こうすることで、ワークロードのピーク時に更新が実行されるのを回避できます。 詳細については、「[Azure Virtual Machines での SQL Server の自動修正 (Resource Manager)](automated-patching.md)」をご覧ください。 |
| **Azure Key Vault の統合** |SQL Server VM に Azure Key Vault を自動的にインストールして構成できます。 詳細については、「[Azure Virtual Machines 上の SQL Server 向け Azure Key Vault 統合の構成 (Resource Manager)](azure-key-vault-integration-configure.md)」をご覧ください。 |
| **柔軟なライセンス** | ライセンス持ち込み (「Azure ハイブリッド特典」とも呼ばれます) から従量課金制のライセンス モデルに、またはその逆に[シームレスに移行](licensing-model-azure-hybrid-benefit-ahb-change.md)することで、コストを節約できます。 | 
| **柔軟なバージョン/エディション** | SQL Server の[バージョン](change-sql-server-version.md)または[エディション](change-sql-server-edition.md)を変更する場合は、SQL Server VM 全体を再デプロイすることなく、Azure portal 内のメタデータを更新できます。  | 


## <a name="management-modes"></a>管理モード

次の 3 つの管理モードによる SQL IaaS 拡張機能の登録を選択できます。 

- **軽量** モードでは、拡張機能バイナリが VM にコピーされますが、エージェントはインストールされず、SQL Server サービスは再起動されません。 軽量モードでは、SQL Server のライセンスの種類とエディションの変更のみがサポートされ、ポータルの管理が制限されます。 このオプションは、複数のインスタンスがあるか、フェールオーバー クラスター インスタンス (FCI) に参加している SQL Server VM に使用します。 簡易モードは、[自動登録](sql-agent-extension-automatic-registration-all-vms.md)機能を使用する場合、または手動登録時に管理の種類が指定されていない場合の既定の管理モードです。 軽量モードを使用する場合、メモリまたは CPU への影響はなく、また関連コストはありません。 最初に軽量モードで SQL Server VM を登録してから、予定メンテナンス期間中にフル モードにアップグレードすることをお勧めします。 

- **フル** モードでは、SQL IaaS Agent が VM にインストールされてすべての機能が提供されますが、SQL Server サービスを再起動する必要があり、またシステム管理者権限が必要です。 単一のインスタンスがある SQL Server VM を管理する場合に使用します。 フル モードでは、メモリと CPU に与える影響が最小の 2 つの Windows サービスがインストールされます。これらは、タスク マネージャーを通して監視できます。 完全管理モードの使用に関連するコストはありません。 

- **NoAgent** モードは Windows Server 2008 にインストールされた SQL Server 2008 および SQL Server 2008 R2 の専用モードです。 NoAgent モードを使用する場合、メモリまたは CPU への影響はありません。 NoAgent 管理モードの使用に関連するコストはありません。SQL Server は再起動されず、エージェントは VM にインストールされません。 

Azure PowerShell を次のように使用して、SQL Server IaaS Agent の現在のモードを確認できます。 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="installation"></a>インストール

SQL Server VM を SQL Server IaaS Agent 拡張機能に登録し、仮想マシン リソースとは "_別の_" リソースである、**SQL 仮想マシン** "_リソース_" をサブスクリプション内に作成します。 拡張機能から SQL Server VM を登録解除すると、**SQL 仮想マシン** "_リソース_" は削除されますが、実際の仮想マシンは削除されません。

Azure portal を介して SQL Server VM の Azure Marketplace イメージをデプロイすると、その SQL Server VM が自動的に拡張機能に登録されます。 ただし、Azure 仮想マシンに SQL Server を自分でインストールすること、またはカスタム VHD から Azure 仮想マシンをプロビジョニングすることを選択する場合は、機能面の利点を活用できるようにするために、SQL Server VM を SQL IaaS Agent 拡張機能に登録する必要があります。 

軽量モードで拡張機能を登録すると、バイナリはコピーされますが、エージェントは VM にインストールされません。 拡張機能が完全管理モードにアップグレードされると、エージェントは VM にインストールされます。 

拡張機能に登録する方法は 3 つあります。 
- [サブスクリプション内の現在の VM および今後の VM をすべて自動登録する場合](sql-agent-extension-automatic-registration-all-vms.md)
- [単一の VM を手動で](sql-agent-extension-manually-register-single-vm.md)
- [複数の VM を一括で手動で](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>名前付きインスタンスのサポート

SQL Server IaaS 拡張機能は、SQL Server の名前付きインスタンスが仮想マシンで使用できる唯一の SQL Server インスタンスである場合に、そのインスタンスで動作します。 複数の SQL Server インスタンスがある VM には、この拡張機能はインストールできません。 

SQL Server の名前付きインスタンスを使用するには、Azure 仮想マシンをデプロイし、1 つの名前付き SQL Server インスタンスをそれにインストールし、その後、[SQL IaaS 拡張機能](sql-agent-extension-manually-register-single-vm.md)にそれを登録します。

または、Azure Marketplace SQL Server イメージで名前付きインスタンスを使用するには、次の手順を実行します。 

   1. Azure Marketplace から SQL Server VM をデプロイします。 
   1. SQL IaaS Agent 拡張機能から SQL Server VM の[登録を解除](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)します。 
   1. SQL Server VM 内で SQL Server を完全にアンインストールします。
   1. SQL Server VM 内の名前付きインスタンスで SQL Server をインストールします。 
   1. [SQL IaaS Agent 拡張機能に VM を登録します](sql-agent-extension-manually-register-single-vm.md#register-with-extension)。 

## <a name="verify-status-of-extension"></a>拡張機能の状態を確認する

Azure portal または Azure PowerShell を使用して、拡張機能の状態を確認します。 

### <a name="azure-portal"></a>Azure portal

Azure portal に拡張機能がインストールされていることを確認します。 

仮想マシンのウィンドウで **[すべての設定]** を選択し、 **[拡張機能]** を選択します。 **SqlIaasExtension** 拡張機能が一覧表示されます。

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


## <a name="frequently-asked-questions"></a>よく寄せられる質問 

**Azure Marketplace の SQL Server イメージからプロビジョニングされた SQL Server VM を登録する必要はありますか?**

いいえ。 Azure Marketplace の SQL Server イメージからプロビジョニングされた VM は自動的に登録されます。 拡張機能への登録が必要になるのは、VM が Azure Marketplace の SQL Server イメージからプロビジョニングされて "*おらず*"、SQL Server を自分でインストールした場合のみです。

**SQL IaaS Agent 拡張機能はすべての顧客が利用できますか?** 

はい。 お客様がご自身の SQL Server VM を拡張機能に登録する必要があるのは、Azure Marketplace の SQL Server イメージを使用せずに自分で SQL Server をインストールした場合やカスタム VHD を購入した場合です。 すべての種類のサブスクリプション (Direct、Enterprise Agreement、Cloud Solution Provider) で所有されているVM は、SQL IaaS Agent 拡張機能に登録できます。

**SQL IaaS Agent 拡張機能への登録時の既定の管理モードは何ですか?**

SQL IaaS Agent 拡張機能に登録する際の既定の管理モードは、"*軽量*" です。 拡張機能への登録時に SQL Server 管理プロパティが設定されていない場合、モードは軽量として設定され、SQL Server サービスは再起動されません。 最初に軽量モードで SQL IaaS Agent 拡張機能に登録してから、予定メンテナンス期間中にフルにアップグレードすることをお勧めします。 同様に、[自動登録機能](sql-agent-extension-automatic-registration-all-vms.md)を使用する場合は、既定の管理も軽量になります。

**SQL IaaS Agent 拡張機能に登録するための前提条件は何ですか?**

VM に SQL Server がインストールされている必要があるという点を除き、SQL IaaS Agent 拡張機能に登録する際の前提条件はありません。 SQL IaaS Agent 拡張機能が完全モードでインストールされている場合は、SQL Server サービスが再起動されるため、メンテナンス期間中に実行することをお勧めします。

**SQL IaaS Agent 拡張機能に登録すると、VM にエージェントがインストールされますか?**

はい。完全管理モードで SQL IaaS Agent 拡張機能に登録すると、VM にエージェントが インストールされます。 軽量モードまたは NoAgent モードで登録する場合は、そうではありません。 

軽量モードで SQL IaaS Agent 拡張機能に登録すると、SQL IaaS Agent 拡張機能の "*バイナリ*" のみが VM にコピーされます。この場合、エージェントはインストールされません。 これらのバイナリは、管理モードが完全にアップグレードされた際、エージェントのインストールに使用されます。


**SQL IaaS Agent 拡張機能に登録すると、自分の VM 上の SQL Server が再起動されますか?**

これは、登録時に指定されたモードによって異なります。 軽量または NoAgent モードが指定されている場合、SQL Server サービスは再起動されません。 ただし、管理モードを完全に指定すると、SQL Server サービスが再起動されます。 自動登録機能では、SQL Server VM が軽量モードで登録されます。ただし、Windows Server のバージョンが 2008 の場合、SQL Server VM は NoAgent モードで登録されます。 

**SQL IaaS Agent 拡張機能に登録する場合、軽量と NoAgent の各管理モードではどのような違いがありますか?** 

NoAgent 管理モードは、Windows Server 2008 上の SQL Server 2008 と SQL Server 2008 R2 で唯一使用可能な管理モードです。 以後のすべてのバージョンの Windows Server で使用可能な 2 つの管理モードとして軽量モードと完全モードがあります。 

NoAgent モードでは、SQL Server のバージョンとエディションのプロパティをお客様が設定する必要があります。 軽量モードでは、VM が照会され、SQL Server インスタンスのバージョンとエディションが確認されます。

**SQL Server のライセンスの種類を指定せずに SQL IaaS Agent 拡張機能に登録できますか?**

いいえ。 SQL IaaS Agent 拡張機能に登録している場合、SQL Server のライセンスの種類は省略可能なプロパティではありません。 すべての管理モード (NoAgent、軽量、フル) で SQL IaaS Agent 拡張機能に登録する際、SQL Server のライセンスの種類を従量課金制または Azure ハイブリッド特典として設定する必要があります。 Developer や Evaluation Edition など、無料版の SQL Server がインストールされている場合は、従量課金制ライセンスで登録する必要があります。 Azure ハイブリッド特典は、Enterprise Edition や Standard Edition などの有料版 SQL Server でのみ使用できます。

**SQL Server IaaS 拡張機能を NoAgent モードから完全モードにアップグレードできますか?**

いいえ。 NoAgent モードでは、管理モードを完全または軽量にアップグレードすることはできません。 これは Windows Server 2008 の技術的な制限です。 最初に OS を Windows Server 2008 R2 以上にアップグレードする必要があります。その後、フル管理モードにアップグレードできます。 

**SQL Server IaaS 拡張機能を軽量モードから完全モードにアップグレードできますか?**

はい。 管理モードを軽量から完全にアップグレードするには、Azure PowerShell または Azure portal を使用します。 実行すると、SQL Server サービスの再起動がトリガーされます。

**SQL Server IaaS 拡張機能を完全モードから NoAgent または軽量管理モードにダウングレードすることはできますか?**

いいえ。 SQL Server IaaS 拡張機能の管理モードのダウングレードはサポートされていません。 管理モードを完全モードから軽量モードまたは NoAgent モードにダウングレードすることはできません。また、軽量モードから NoAgent モードにダウングレードすることもできません。 

管理モードを完全管理から変更するには、SQL 仮想マシン "_リソース_" を削除して SQL IaaS Agent 拡張機能から SQL Server VM の[登録を解除](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)し、別の管理モードでその SQL Server VM を SQL IaaS Agent 拡張機能に再登録します。

**Azure portal から SQL IaaS Agent 拡張機能に登録できますか?**

いいえ。 Azure portal では SQL IaaS Agent 拡張機能への登録を利用できません。 SQL IaaS Agent 拡張機能への登録は、Azure CLI または Azure PowerShell でのみサポートされます。 

**SQL Server をインストールする前に、VM を SQL IaaS Agent 拡張機能に登録できますか?**

いいえ。 SQL IaaS Agent 拡張機能に正常に登録するには、VM に少なくとも 1 つの SQL Server (データベース エンジン) インスタンスが必要です。 VM 上に SQL Server インスタンスが存在しない場合、新しい Microsoft.SqlVirtualMachine リソースはエラー状態になります。

**複数の SQL Server インスタンスがある場合、VM を SQL IaaS Agent 拡張機能に登録できますか?**

はい。 SQL IaaS Agent 拡張機能では、1 つの SQL Server (データベース エンジン) インスタンスのみ登録されます。 複数のインスタンスがある場合、SQL IaaS Agent 拡張機能では、既定の SQL Server インスタンスが登録されます。 既定のインスタンスがない場合、軽量モードでの登録のみがサポートされます。 軽量から完全管理モードにアップグレードするには、既定の SQL Server インスタンスが存在するか、VM に名前付き SQL Server インスタンスが 1 つだけ存在する必要があります。

**SQL Server フェールオーバー クラスター インスタンスを SQL IaaS Agent 拡張機能に登録できますか?**

はい。 Azure VM 上の SQL Server フェールオーバー クラスター インスタンスは SQL IaaS Agent 拡張機能に軽量モードで登録できます。 ただし、SQL Server フェールオーバー クラスター インスタンスを完全管理モードにアップグレードすることはできません。

**Always On 可用性グループが構成されている場合、自分の VM を SQL IaaS Agent 拡張機能に登録できますか?**

はい。 Always On 可用性グループ構成に参加している場合、Azure VM 上の SQL Server インスタンスを SQL IaaS Agent 拡張機能に登録することに制限はありません。

**SQL IaaS Agent 拡張機能に登録する場合、または完全管理モードにアップグレードする場合、どのようなコストがかかりますか?**

[なし] : SQL IaaS Agent 拡張機能への登録に関連する料金も、3 種類のいずれの管理モードの使用に関連する料金もかかりません。 ご利用の SQL Server VM を拡張機能を使用して管理する場合は、完全に無料です。 

**さまざまな管理モードを使用すると、パフォーマンスにどのような影響がありますか?**

*NoAgent* および *軽量* 管理性モードを使用する場合、影響はありません。 OS にインストールされている 2 つのサービスから、*完全* 管理モードを使用する場合の影響は最小限に抑えられます。 これらは、タスク マネージャーを使用して監視し、組み込みの Windows サービス コンソールに表示することができます。 

これらの 2 つのサービス名は次のとおりです。
- `SqlIaaSExtensionQuery` (表示名 - `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (表示名 - `Microsoft SQL Server IaaS Agent`)

**拡張機能を削除する方法**

拡張機能を削除するには、SQL IaaS Agent 拡張機能から SQL Server VM の[登録を解除](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)します。 

## <a name="next-steps"></a>次のステップ

SQL Server IaaS 拡張機能を Azure VM 上の SQL Server にインストールする方法については、[自動インストール](sql-agent-extension-automatic-registration-all-vms.md)、 [単一の VM の登録](sql-agent-extension-manually-register-single-vm.md)、または [VM の一括登録](sql-agent-extension-manually-register-vms-bulk.md) に関する記事を参照してください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「[Azure Virtual Machines における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)」を参照してください。
