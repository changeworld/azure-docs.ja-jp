---
title: Azure VM の SQL Server リリース ノート | Microsoft Docs
description: Azure VM 上の SQL Server の新機能と機能改善について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: 7c854759e9a0ba13b698dd07dabb25100895bc85
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766879"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure 仮想マシンの SQL Server リリース ノート

Azure では、SQL Server のイメージを組み込んだ仮想マシンをデプロイできます。 この記事では、Azure 仮想マシンにデプロイされた最新版の SQL Server に搭載された新機能と機能改善について説明します。 

## <a name="december-2018"></a>2018 年 12 月

| **変更点** | 詳細 |
| --- | --- |
| **新しい SQL クラスター グループ リソース プロバイダー** | Windows フェールオーバー クラスターに関するメタデータを定義する新しいリソース プロバイダー (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) があります。 SQL Server VM を *SqlVirtualMachineGroups* に参加させることで、Windows フェールオーバー クラスター サービスをブートストラップし、VM をクラスターに参加させます。  |
|**Azure クイック スタート テンプレートを使用して、可用性グループのデプロイの設定を自動化する** |2 つの Azure クイック スタート テンプレートを使用して、Windows フェールオーバー クラスターを作成し、SQL Server VM をそれに参加させ、リスナーを作成し、内部ロード バランサーを構成することができるようになりました。 詳細については、「[Create WSFC, listener, and configure ILB for an Always On availability group on a SQL Server VM with Azure Quickstart Template](virtual-machines-windows-sql-availability-group-quickstart-template.md)」(WSFC、リスナーを作成し、Azure クイック スタート テンプレートを使用して、SQL Server VM に Always On 可用性グループ用の ILB を構成する) をご覧ください。 | 
| **SQL VM リソース プロバイダーの自動登録** | 今月以降にデプロイされる SQL Server VM は、自動的に新しい SQL Server リソース プロバイダーに登録されます。 今月より前にデプロイされた SQL Server VM は、手動で登録する必要があります。 詳細については、「[既存の SQL VM を新しいリソース プロバイダーに登録する](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider)」をご覧ください。|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>2018 年 11 月

| **変更点** | 詳細 |
| --- | --- |
| **新しい SQL VM リソース プロバイダー** |  SQL Server VM の管理が向上する、SQL Server VM 用の新しいリソース プロバイダー (Microsoft.SqlVirtualMachine) があります。 自分の VM を登録する方法の詳細については、「[既存の SQL VM を新しいリソース プロバイダーに登録する](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider)」を参照してください。 |
|**ライセンス モデルを切り替える** |Azure CLI または PowerShell を使用して、SQL VM のライセンス モデルを従量制と持ち込みの間で切り替えることができます。 詳細については、[SQL VM のライセンス モデルを変更する方法](virtual-machines-windows-sql-ahb.md)に関するページを参照してください。 | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>その他のリソース

**Windows VM**:

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM のプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)
* [Azure VM の SQL Server へのデータベースの移行](virtual-machines-windows-migrate-sql.md)
* [Azure 仮想マシンにおける SQL Server の高可用性と障害復旧](virtual-machines-windows-sql-high-availability-dr.md)
* [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-windows-sql-performance.md)
* [Azure Virtual Machines における SQL Server のアプリケーション パターンと開発計画](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Linux VM**:

* [Linux VM における SQL Server の概要](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux VM のプロビジョニング](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [よく寄せられる質問 (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server on Linux のドキュメント](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
