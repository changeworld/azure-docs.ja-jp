---
title: Azure NetApp Files のパフォーマンスに関する FAQ | Microsoft Docs
description: Azure NetApp Files のパフォーマンスについてのよくあるご質問 (FAQ) に回答します。
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: e7b489a2f9960b39fcb8035889b47468c2af0478
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269950"
---
# <a name="performance-faqs-for-azure-netapp-files"></a>Azure NetApp Files のパフォーマンスに関するよくあるご質問

この記事では、Azure NetApp Files のパフォーマンスについてのよくあるご質問 (FAQ) に回答します。

## <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Azure NetApp Files のパフォーマンスを最適化したり調整したりするには、どうすればよいですか?

パフォーマンス要件に従って、次の操作を実行できます。 
- 仮想マシンのサイズが適切であることを示します。
- VM の高速ネットワークを有効にします。
- 容量プールに使用したいサービス レベルとサイズを選択します。
- 容量とパフォーマンスに使用したいクォータ サイズのボリュームを作成します。

## <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Azure NetApp Files のスループット ベースのサービス レベルを IOPS に変換するには、どうすればよいですか?

次の数式を使用して、MB/秒を IOPS に変換できます。  

`IOPS = (MBps Throughput / KB per IO) * 1024`

## <a name="how-do-i-change-the-service-level-of-a-volume"></a>ボリュームのサービス レベルを変更するにはどうすればよいですか?

既存のボリュームのサービス レベルは変更できます。それには、目的のボリュームで使用したい[サービス レベル](azure-netapp-files-service-levels.md)を使用中の別の容量プールに、目的のボリュームを移動します。 「[ボリュームのサービス レベルを動的に変更する](dynamic-change-volume-service-level.md)」を参照してください。 

## <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Azure NetApp Files のパフォーマンスを監視するには、どうすればよいですか?

Azure NetApp Files には、ボリュームのパフォーマンス メトリックが用意されています。 また、Azure Monitor を使用して Azure NetApp Files の使用状況のメトリックを監視することもできます。  Azure NetApp Files のパフォーマンス メトリックの一覧については、「[Azure NetApp Files のメトリック](azure-netapp-files-metrics.md)」を参照してください。

## <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>NFSv4.1 での Kerberos のパフォーマンスへの影響はどのようなものですか?

NFSv 4.1 のセキュリティ オプション、テスト済みのパフォーマンス ベクトル、予想されるパフォーマンスへの影響については、[NFSv 4.1 ボリュームでの Kerberos のパフォーマンスへの影響](performance-impact-kerberos.md)に関するページを参照してください。 

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files では SMB ダイレクトはサポートされていますか?

いいえ。Azure NetApp Files では SMB ダイレクトはサポートされていません。 

## <a name="is-nic-teaming-supported-in-azure"></a>Azure では NIC チーミングはサポートされていますか?

NIC チーミングは Azure ではサポートされていません。 Azure 仮想マシンでは複数のネットワーク インターフェイスがサポートされていますが、それらは物理的な構成ではなく論理的な構成を表します。 そのため、フォールト トレランスは提供されません。  また、Azure 仮想マシンで利用できる帯域幅は、個々のネットワーク インターフェイスではなく、マシン自体に対して計算されます。

## <a name="are-jumbo-frames-supported"></a>ジャンボ フレームはサポートされていますか?

Azure 仮想マシンでは、ジャンボ フレームはサポートされていません。

## <a name="next-steps"></a>次のステップ  

- [NFSv4.1 ボリュームでの Kerberos のパフォーマンスに対する影響](performance-impact-kerberos.md)
- [Azure NetApp Files のパフォーマンスに関する考慮事項](azure-netapp-files-performance-considerations.md    )
- [Azure NetApp Files のパフォーマンス ベンチマークのテスト レコメンデーション](azure-netapp-files-performance-metrics-volumes.md )
- [Linux のパフォーマンス ベンチマーク](performance-benchmarks-linux.md)
- [NFSv4.1 ボリュームでの Kerberos のパフォーマンスに対する影響](performance-impact-kerberos.md)
- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [ネットワークに関する FAQ](faq-networking.md)
- [セキュリティに関する FAQ](faq-security.md)
- [NFS に関する FAQ](faq-nfs.md)
- [SMB に関する FAQ](faq-smb.md)
- [容量管理に関する FAQ](faq-capacity-management.md)
- [データの移行と保護に関する FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
- [アプリケーションの回復性に関する FAQ](faq-application-resilience.md)
- [統合に関する FAQ](faq-integration.md)