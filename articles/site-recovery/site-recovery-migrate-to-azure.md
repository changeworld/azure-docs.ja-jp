---
title: "Site Recovery を使用してオンプレミスの VM と物理サーバーを Azure に移行する | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して、オンプレミスの VM と物理サーバーを Azure に移行する方法を説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 423a1727efb0e1fd54eb0f8d5971ace3f8efc6cb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Site Recovery を使用した Azure への移行

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、オンプレミスの仮想マシン (VM) と物理サーバーを Azure VM に移行する方法について説明します。

## <a name="before-you-start"></a>開始する前に

Azure への移行に必要な手順の短い概要をこのビデオで確認します。
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]


## <a name="what-do-we-mean-by-migration"></a>移行が意味すること

Site Recovery をデプロイしてオンプレミス VM や物理サーバーをレプリケートし、移行することができます。

- レプリケートする際は、オンプレミスのマシンが定期的に Azure にレプリケートされるように構成します。 その後障害が発生したときは、マシンをオンプレミス サイトから Azure にフェールオーバーし、そこからこれらのマシンにアクセスします。 オンプレミス サイトが再度使用可能になると、Azure からフェールバックします。
- 移行に Site Recovery を使用する場合は、オンプレミスのマシンを Azure にレプリケートします。 次に、オンプレミス サイトから Azure にフェールオーバーし、移行プロセスを完了します。 フェールバックは必要ありません。  

## <a name="what-can-site-recovery-migrate"></a>Site Recovery で移行できるもの

次のようにすることができます。

- **オンプレミスからの移行**: オンプレミスの Hyper-V VM、VMware VM、物理サーバーを Azure に移行します。 移行後、オンプレミスのマシンで実行されているワークロードが Azure VM で実行されるようになります。 
- **Azure 内での移行**: Azure リージョン間で Azure VM を移行します。 
- **AWS の移行**: AWS Windows インスタンスを Azure IaaS VM に移行します。 

## <a name="migrate-from-on-premises-to-azure"></a>オンプレミスから Azure への移行

オンプレミスの VMware VM、Hyper-V VM、物理サーバーを移行するには、完全なレプリケーションの場合とほぼ同じ手順に従います。 


## <a name="migrate-between-azure-regions"></a>Azure リージョン間での移行

リージョン間で Azure VM を移行するには、完全なレプリケーションの場合とほぼ同じ手順に従います。

1. 移行するマシンの[レプリケーションを有効](azure-to-azure-tutorial-enable-replication.md)にします。
2. [簡単なテスト フェールオーバーを実行](azure-to-azure-tutorial-dr-drill.md)して、すべて問題なく動作していることを確認します。
3. **[移行の完了]** オプションを使用して、[計画外フェールオーバーを実行](azure-to-azure-tutorial-failover-failback.md)します。
4. 移行が完了したら、移行先の Azure リージョンからセカンダリ リージョンに[ディザスター リカバリーのためのレプリケーションを設定](site-recovery-azure-to-azure-after-migration.md)できます。



## <a name="migrate-aws-to-azure"></a>Azure への AWS の移行

AWS インスタンスを Azure VM に移行できます。
- このシナリオでは、移行のみがサポートされます。 つまり、AWS インスタンスをレプリケートして、別のリージョンにフェールオーバーできますが、フェールバックすることはできません。
- AWS インスタンスは、移行のために物理サーバーと同じ方法で処理されます。 Recovery Services コンテナーを設定し、レプリケーションを管理するオンプレミスの構成サーバーをデプロイして、そのサーバーをコンテナーに追加し、レプリケーション設定を指定します。
- 移行するマシンのレプリケーションを有効にし、簡単なテスト フェールオーバーを実行します。 **[移行の完了]** オプションを使用して、計画外フェールオーバーを実行します。






## <a name="next-steps"></a>次のステップ

- [オンプレミスのマシンを Azure に移行する](tutorial-migrate-on-premises-to-azure.md)
- [VM を 1 つの Azure リージョンから別のリージョンに移行する](site-recovery-migrate-azure-to-azure.md)
- [AWS を Azure に移行する](tutorial-migrate-aws-to-azure.md)
