---
title: Azure Site Recovery を使用した Azure へのディザスター リカバリーから VMware VM ディスクを除外する
description: Azure Site Recovery を使用した Azure へのレプリケーションから VMware VM ディスクを除外する方法。
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.author: sharrai
ms.date: 05/27/2021
ms.openlocfilehash: 6aa4776ed1666857ba2873351c24dc561edb5890
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576966"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Azure への VMware VM レプリケーションからディスクを除外する

この記事では、ディザスター リカバリーのために VMware VM を Azure にレプリケートするときに、ディスクを除外する方法について説明します。 レプリケーションからディスクを除外する理由はさまざまです。

- 除外されるディスク上のチャーンされた重要でないデータがレプリケートされないようにする。
- レプリケートする必要のないディスクを除外することで、消費されるレプリケーションの帯域幅またはターゲット側のリソースを最適化する。
- 必要のないデータをレプリケートしないことで、ストレージとネットワークのリソースを節約する。

レプリケーションからディスクを除外する前に:

- ディスクの除外の詳細については、[こちら](exclude-disks-replication.md)をご覧ください。
- ディスクの除外がレプリケーション、フェールオーバー、およびフェールバックにどのように影響するかを示す[一般的な除外シナリオ](exclude-disks-replication.md#typical-scenarios)と[例](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)の記事を参照してください。

## <a name="before-you-start"></a>開始する前に

 開始する前に、次の点に注意してください。

- **[Replication]\(レプリケーション\)** :既定では、マシンのすべてのディスクがレプリケートされます。
- **[ディスクの種類]** : レプリケーションから除外できるのは、ベーシック ディスクだけです。 オペレーティング システム ディスクまたはダイナミック ディスクは除外できません。
- **Mobility Service**:レプリケーションからディスクを除外するには、レプリケーションを有効にする前に、マシンに Mobility Service を手動でインストールする必要があります。 プッシュ インストールを使用することはできません。この方法では、レプリケーションが有効になった後にのみ、VM に Mobility Service がインストールされるためです。  
- **ディスクの追加、削除、除外**:レプリケーションを有効にした後に、レプリケートするディスクの追加、削除、または除外を行うことはできません。 ディスクの追加、削除、または除外を行うには、マシンの保護を無効にし、再度有効にする必要があります。
- **フェールオーバー**:フェールオーバー後、フェールオーバーされたアプリが機能するために除外されたディスクが必要な場合は、それらのディスクを手動で作成する必要があります。 別の方法として、Azure Automation を復旧計画に組み込んで、マシンのフェールオーバー時にディスクを作成することもできます。
- **フェールバック Windows**:フェールオーバー後にオンプレミス サイトにフェールバックする場合、Azure で手動で作成した Windows ディスクはフェールバックされません。 たとえば、3 つのディスクをフェールオーバーし、Azure VM 上に 2 つのディスクを直接作成した場合、フェールオーバーされた 3 つのディスクだけがフェールバックされます。
- **フェールバック Linux**:Linux マシンのフェールバックの場合、Azure で手動で作成したディスクはフェールバックされます。 たとえば、3 つのディスクをフェールオーバーし、Azure VM 上に 2 つのディスクを直接作成した場合、5 つのディスクがすべてフェールバックされます。 フェールバックまたは VM の再保護で手動で作成されたディスクを除外することはできません。



## <a name="exclude-disks-from-replication"></a>レプリケーションからディスクを除外する

1. VMware VM の [レプリケーションを有効にする](./hyper-v-azure-tutorial.md)場合、レプリケートする VM を選択した後、 **[レプリケーションを有効にする]**  >  **[プロパティ]**  >  **[プロパティの構成]** ページで、 **[レプリケートするディスク]** 列を確認します。 既定では、すべてのディスクがレプリケーションの対象として選択されます。
2. 特定のディスクをレプリケートしない場合は、 **[レプリケートするディスク]** で、除外するディスクの選択を解除します。 

    ![レプリケーションからディスクを除外する](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>次のステップ
デプロイをセットアップし、実行状態にできたら、各種フェールオーバーの [詳細を確認](failover-failback-overview.md) します。
