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
ms.openlocfilehash: 52036d8f5d25fc4a4f2d1b602428e9cba4762b7f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993135"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Azure 仮想マシンの SQL Server リリース ノート

Azure では、SQL Server のイメージを組み込んだ仮想マシンをデプロイできます。 この記事では、Azure 仮想マシンにデプロイされた最新版の SQL Server に搭載された新機能と機能改善について説明します。 


## <a name="november-2018"></a>2018 年 11 月
- **新しい SQL リソース プロバイダー**:SQL VM に、VM の管理を効率的にする新しいリソース プロバイダーが追加されました。 自分の VM を登録する方法の詳細については、「[既存の SQL VM を新しいリソース プロバイダーに登録する](virtual-machines-windows-sql-ahb.md#register-existing-sql-vm-with-new-resource-provider)」を参照してください。
- **ライセンス モデルを切り替える**: Azure CLI または Powershell を使用し、SQL VM に対して従量制/ライセンス持ち込みモデルを切り替えることができます。 詳細については、[SQL VM のライセンス モデルを変更する方法](virtual-machines-windows-sql-ahb.md)に関するページを参照してください。



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
