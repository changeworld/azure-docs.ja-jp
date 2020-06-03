---
title: Azure Virtual Machines 上の SQL Server に関するドキュメントの変更 | Microsoft Docs
description: Azure VM 上の SQL Server の新機能と機能改善について説明します。
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: f87d72df977e98c60948389d794eb102ac08f8d2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032473"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SQL Server に関するドキュメントの変更
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure では、SQL Server のイメージを組み込んだ仮想マシン (VM) をデプロイできます。 この記事では、[Azure Virtual Machines 上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) の最新リリースで導入された新機能と機能強化に関連するドキュメントの変更をまとめます。 


## <a name="january-2020"></a>2020 年 1 月

| [変更点] | 詳細 |
| --- | --- |
| **Azure Government のサポート** | [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) クラウドでホストされている仮想マシンの SQL VM リソース プロバイダーに SQL Server 仮想マシンを登録できるようになりました。 | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|[変更点] | 詳細 |
 --- | --- |
| **Azure での無料 DR レプリカ** | [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)がある場合は、オンプレミスの SQL Server インスタンス用に Azure でディザスター リカバリー用の[無料のパッシブ インスタンス](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)をホストできます。 | 
| **リソースプロバイダーの一括登録** | リソースプロバイダーに SQL 仮想マシンを[一括登録](sql-vm-resource-provider-bulk-register.md)できるようになりました。 | 
|**パフォーマンスが最適化されたストレージ構成** | 新しい SQL Server VM を作成するときに、[ストレージの構成を完全にカスタマイズする](storage-configuration.md#new-vms)ことができるようになりました。 |
|**FCI 用の Premium ファイル共有** | [記憶域スペース ダイレクト](failover-cluster-instance-storage-spaces-direct-manually-configure.md)の元の方法ではなく、[Premium ファイル共有](failover-cluster-instance-premium-file-share-manually-configure.md)を使用してフェールオーバー クラスター インスタンスを作成できるようになりました。 
| **Azure の専用ホスト** | SQL Server VM は、[Azure 専用ホスト](dedicated-host.md)で実行できます。 | 
| **SQL VM を別のリージョンに移動する** | Azure Site Recovery を使用して、[異なるリージョン間で SQL Server VM を移行](move-sql-vm-different-region.md)します。 |
|  **新しい SQL IaaS インストール モード** | SQL Server サービスの再開を回避するため、SQL Server IaaS 拡張機能を[軽量モード](sql-server-iaas-agent-extension-automate-management.md)でインストールできるようになりました。  |
| **SQL Server エディションの変更** | SQL Server VM の [エディション プロパティ](change-sql-server-edition.md)を変更できるようになりました。 |
| **SQL VM リソース プロバイダーの変更** | 新しい SQL IaaS モードを使用して、[SQL Server VM を SQL VM リソース プロバイダーに登録](sql-vm-resource-provider-register.md)できます。 この機能には、[Windows Server 2008 のイメージ](sql-vm-resource-provider-register.md#management-modes)が含まれます。|
| **Azure ハイブリッド特典を使用するライセンス持ち込みイメージ** | Azure Marketplace からデプロイされたライセンス持ち込みイメージを使用して、[ライセンスの種類を従量課金制に](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)切り替えることができるようになりました。| 
| **Azure portal での新しい SQL Server VM の管理** | Azure portal で SQL Server VM を管理する新しい方法が導入されました。 詳細については、「[Azure portal で SQL Server VM を管理する](manage-sql-vm-portal.md)」を参照してください。  | 
| **SQL Server 2008/2008 R2 の延長サポート** | "*そのまま*" Azure VM に移行することで、SQL Server 2008 および SQL Server 2008 R2 の[サポートを延長](sql-server-2008-extend-end-of-support.md)します。 | 
| **カスタム イメージのサポートの可否** | OS と SQL イメージをカスタマイズするために、[SQL Server IaaS 拡張機能](sql-server-iaas-agent-extension-automate-management.md#installation)をインストールできるようになりました。これにより、機能が制限された[柔軟なライセンス](licensing-model-azure-hybrid-benefit-ahb-change.md)が提供されます。 カスタム イメージを SQL リソース プロバイダーに登録するときに、ライセンスの種類として "AHUB" を指定します。 そうしないと、登録は失敗します。 | 
| **名前付きインスタンスのサポートの可否** | 既定のインスタンスが適切にアンインストールされている場合、名前付きインスタンスで [SQL Server IaaS 拡張機能](sql-server-iaas-agent-extension-automate-management.md#installation)を使用できるようになりました。 | 
| **ポータルの機能強化** | SQL Server VM をデプロイするための Azure portal エクスペリエンスは、より使いやすくなるように改良されています。 詳細については、SQL Server VM のデプロイに関する簡単な[クイックスタート](sql-vm-create-portal-quickstart.md)と、より包括的な[ハウツー](create-sql-vm-portal.md) ガイドを参照してください。|
| **ポータルの改善** | [Azure portal](licensing-model-azure-hybrid-benefit-ahb-change.md#vms-already-registered-with-the-resource-provider) を使用して、SQL Server VM のライセンス モデルを従量課金制からライセンス持ち込みに変更できるようになりました。|
| **Azure SQL Server VM CLI を使用した可用性グループのデプロイの簡略化** | 可用性グループを Azure の SQL Server VM にデプロイすることが以前より簡単になりました。 [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) を使用すると、Windows フェールオーバー クラスター、内部ロード バランサー、および可用性グループのリスナーのすべてをコマンド ラインから作成できます。 詳細については、[Azure SQL Server VM CLI を使用して Azure VM 上で SQL Server のための Always On 可用性グループを構成する](availability-group-az-cli-configure.md)方法のページを参照してください。 | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 [変更点] | 詳細 |
| --- | --- |
|  **SQL Server クラスターの新しいリソース プロバイダー** | Windows フェールオーバー クラスターのメタデータを定義する新しいリソース プロバイダー (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)。 SQL Server VM を *SqlVirtualMachineGroups* に参加させることで、Windows Server フェールオーバー クラスター (WSFC) サービスをブートストラップし、VM をクラスターに参加させます。  |
| **Azure クイックスタート テンプレートを使用して自動化された可用性グループのデプロイの設定** |2 つの Azure クイックスタート テンプレートを使用して、Windows フェールオーバー クラスターを作成し、SQL Server VM をそれに参加させ、リスナーを作成し、内部ロード バランサーを構成することができるようになりました。 詳細については、「[Azure クイックスタート テンプレートを使用して Azure VM で SQL Server の Always On 可用性グループを構成する](availability-group-quickstart-template-configure.md)」を参照してください。 | 
| **SQL VM リソース プロバイダーへの自動登録** | 今月以降にデプロイされる SQL Server VM は、自動的に新しい SQL Server リソース プロバイダーに登録されます。 今月より前にデプロイされた SQL Server VM は、まだ手動で登録する必要があります。 詳細については、「[Azure の SQL Server 仮想マシンを SQL VM リソース プロバイダーに登録する](sql-vm-resource-provider-register.md)」を参照してください。|
|**新しい SQL VM リソース プロバイダー** |  新しいリソース プロバイダー (Microsoft.SqlVirtualMachine) を使用すると、SQL Server VM の管理が強化されます。 VM の登録の詳細については、「[Azure の SQL Server 仮想マシンを SQL VM リソース プロバイダーに登録する](sql-vm-resource-provider-register.md)」を参照してください。 |
|**ライセンス モデルを切り替える** | Azure CLI または PowerShell を使用して、SQL Server VM のライセンス モデルを従量制と持ち込みの間で切り替えることができるようになりました。 詳細については、[Azure の SQL Server 仮想マシンのライセンス モデルを変更する方法](licensing-model-azure-hybrid-benefit-ahb-change.md)に関するページを参照してください。 | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>その他のリソース

**Windows VM**:

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server Windows VM のプロビジョニング](create-sql-vm-portal.md)
* [Azure VM の SQL Server へのデータベースの移行](migrate-to-vm-from-sql-server.md)
* [Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](performance-guidelines-best-practices.md)
* [Azure Virtual Machines における SQL Server のアプリケーション パターンと開発計画](application-patterns-development-strategies.md)

**Linux VM**:

* [Linux VM における SQL Server の概要](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [SQL Server Linux 仮想マシンのプロビジョニング](../linux/sql-vm-create-portal-quickstart.md)
* [よく寄せられる質問 (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server on Linux のドキュメント](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
