---
title: "Site Recovery と Azure への移行 |Microsoft ドキュメント"
description: "この記事は、Azure Site Recovery と Azure に移行する仮想マシンと物理サーバーの概要を示します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/05/2017
ms.author: raynew
ms.openlocfilehash: f4dfe430fba51bd009431ca72279a21be55e3a40
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Site Recovery と Azure への移行します。

仮想マシンと物理サーバーの移行のための Azure Site Recovery サービスを使用して概要については、この記事を参照します。

Site Recovery は、オンプレミスの物理サーバーとクラウド (Azure) へ、またはセカンダリ データ センターへの仮想マシンのレプリケーションの調整により、BCDR 戦略に貢献する Azure サービスです。 1 次拠点で障害が発生すると、フェールオーバーするアプリとワークロードを使用して、2 次拠点にします。 通常の操作に戻ったとき、プライマリ ロケーションに失敗します。 詳細について[Site Recovery は何ですか。](site-recovery-overview.md) 移行する、既存のワークロードをオンプレミス、クラウドの旅を高速化し、Azure が提供する機能の配列を利用するのに Azure Site Recovery を使用することもできます。

移行を実行する方法の簡単な概要は、このビデオを参照してください。
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]

この記事での展開の説明、 [Azure ポータル](https://portal.azure.com)です。 [Azure クラシック ポータル](https://manage.windowsazure.com/)既存の Site Recovery コンテナーを維持するために使用できますが、新しいコンテナーを作成することはできません。

この記事の下部にあるコメントを投稿します。 技術的な質問、 [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)です。


## <a name="what-do-we-mean-by-migration"></a>移行で新機能のこと。

内部設置型 Vm と Azure またはセカンダリ サイトの物理サーバーのレプリケーションで、Site Recovery をデプロイすることができます。 マシンをレプリケートするフェールオーバー、プライマリ サイトからの停止が発生し、復旧時にプライマリ サイトに戻るには失敗します。 さらに、ユーザーが Azure Vm としてアクセスできるように、azure Vm と物理サーバーを移行するのに Site Recovery を使用することができます。 移行では、レプリケーション、およびプライマリ サイトから Azure、およびジェスチャを移行の完了からのフェールオーバーが必要です。

## <a name="what-can-site-recovery-migrate"></a>Site Recovery の移行とできますか。

次の操作を行います。

- 内部設置型 HYPER-V 仮想マシン、VMware 仮想マシンおよび物理サーバーを Azure Vm で実行するを実行しているワークロードを移行します。 実行することもできます完全なレプリケーションとこのシナリオにフェールバックします。
- 移行[Azure IaaS Vm](site-recovery-migrate-azure-to-azure.md) Azure リージョン間でします。 このシナリオでは、フェールバックがサポートされていないことを意味で、現在のみの移行がサポートされます。
- 移行[AWS Windows インスタンス](site-recovery-migrate-aws-to-azure.md)Azure IaaS Vm にします。 このシナリオでは、フェールバックがサポートされていないことを意味で、現在のみの移行がサポートされます。

## <a name="migrate-on-premises-vms-and-physical-servers"></a>内部設置型 Vm と物理サーバーを移行します。

移行する内部設置型 HYPER-V 仮想マシン、VMware 仮想マシンおよび物理サーバーで定期的なレプリケーションを使用するものとほぼ同じ手順を実行するください。

1. Recovery Services コンテナーを設定します。
2. 必要な構成管理サーバー (VMware、VMM では、HYPER-V の移行するに応じて) は、資格情報コンテナーに追加し、レプリケーション設定を指定します。
3. 移行するマシンのレプリケーションを有効にします。
4. 最初の移行後は、すべてが動作することを確認する簡単なテスト フェールオーバーを実行します。
5. に応じて計画または計画外フェールオーバーを使用するレプリケーション環境が動作していることを確認したら、[サポートされている機能](site-recovery-failover.md)のシナリオです。 可能な場合は、計画されたフェールオーバーを使用することをお勧めします。
6. 移行する場合、フェールオーバーをコミットするか、削除する必要はありません。 代わりに、選択、**移行の完了**を移行する各マシンのオプションです。
     - **アイテムのレプリケート**、VM を右クリックし、クリックして**移行の完了**です。 をクリックして**OK**を完了します。 完全な移行ジョブを監視することによってで VM のプロパティで進行状況を追跡できます**サイトの回復ジョブ**です。
     - **移行の完了**アクションが移行プロセスを完了すると、マシンのレプリケーションが削除およびマシンの Site Recovery の課金を停止します。

![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

## <a name="migrate-between-azure-regions"></a>Azure リージョン間での移行します。

Site Recovery を使用するリージョン間で Azure Vm を移行することができます。 このシナリオでのみの移行はサポートされています。 つまり、Azure Vm のレプリケートし、それらを別の地域にフェールオーバーすることができますが、フェールバックことはできません。 Recovery Services コンテナーを設定するこのシナリオでは、レプリケーションを管理し、資格情報コンテナーに追加し、レプリケーション設定を指定する、内部設置型の構成サーバーを展開します。 を移行するマシンのレプリケーションを有効にして、簡単なテスト フェールオーバーを実行します。 計画外のフェールオーバーを実行して、**移行の完了**オプション。

## <a name="migrate-aws-to-azure"></a>AWS を Azure に移行します。

AWS インスタンスは、Azure Vm に移行できます。 このシナリオでのみの移行はサポートされています。 つまり、AWS インスタンスのレプリケートし、それらを Azure にフェールオーバーすることができますが、フェールバックことはできません。 AWS インスタンスは、移行のための物理サーバーと同じ方法で処理されます。 復旧サービス コンテナーを設定、レプリケーションを管理し、資格情報コンテナーに追加し、レプリケーション設定を指定する、内部設置型の構成サーバーをデプロイするとします。 を移行するマシンのレプリケーションを有効にして、簡単なテスト フェールオーバーを実行します。 計画外のフェールオーバーを実行して、**移行の完了**オプション。




## <a name="next-steps"></a>次のステップ

- [VMware 仮想マシンを Azure に移行します。](site-recovery-vmware-to-azure.md)
- [VMM クラウド内の HYPER-V で Vm を Azure に移行します。](site-recovery-vmm-to-azure.md)
- [VMM なし HYPER-V Vm を Azure に移行します。](site-recovery-hyper-v-site-to-azure.md)
- [Azure リージョン間での Azure Vm を移行します。](site-recovery-migrate-azure-to-azure.md)
- [AWS インスタンスを Azure に移行します。](site-recovery-migrate-aws-to-azure.md)
- [レプリケーションを有効に移行したマシンを準備する](site-recovery-azure-to-azure-after-migration.md)災害復旧に備えて別のリージョンをする必要があります。
- によって、ワークロードの保護を開始[Azure の仮想マシンをレプリケートします。](site-recovery-azure-to-azure.md)
