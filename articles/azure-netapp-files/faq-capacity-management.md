---
title: Azure NetApp Files についてよく寄せられる質問 | Microsoft Docs
description: Azure NetApp Files の容量管理についてのよくあるご質問 (FAQ) に回答します。
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: 1f28b7b590ef2a7a3894f9eff7eff7199167afc3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269986"
---
# <a name="capacity-management-faqs-for-azure-netapp-files"></a>Azure NetApp Files の容量管理に関するよくあるご質問

この記事では、Azure NetApp Files の容量管理についてのよくあるご質問 (FAQ) に回答します。

## <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Azure NetApp Files の容量プールとボリュームの使用状況を監視するには、どうすればよいですか? 

Azure NetApp Files には、容量プールとボリュームの使用状況のメトリックが用意されています。 また、Azure Monitor を使用して Azure NetApp Files の使用状況を監視することもできます。 詳細については、「[Azure NetApp Files のメトリック](azure-netapp-files-metrics.md)」を参照してください。 

## <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>ディレクトリがサイズ制限に近づいているかどうかを確認するにはどうすればよいですか?

ディレクトリがディレクトリ メタデータの[最大サイズの制限](azure-netapp-files-resource-limits.md#resource-limits) (320 MB) に近づいているかどうかを確認するには、クライアントから `stat` コマンドを使用できます。
上限と計算方法については「[Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md#directory-limit)」をご覧ください。 

## <a name="does-snapshot-space-count-towards-the-usable--provisioned-capacity-of-a-volume"></a>スナップショット領域は、ボリュームの使用可能な容量またはプロビジョニングされた容量にカウントされますか?

はい。[消費されたスナップショット容量](azure-netapp-files-cost-model.md#capacity-consumption-of-snapshots)は、ボリューム内のプロビジョニングされた領域にカウントされます。 ボリュームがいっぱいになった場合は、次のアクションを実行することを検討してください。

* [ボリュームのサイズを変更する](azure-netapp-files-resize-capacity-pools-or-volumes.md)。
* ホストしているボリューム内の領域を解放するために、[以前のスナップショットを削除する](snapshots-delete.md)。 

## <a name="does-azure-netapp-files-support-auto-grow-for-volumes-or-capacity-pools"></a>Azure NetApp Files では、ボリュームまたは容量プールの自動拡張をサポートしていますか?

いいえ。Azure NetApp Files ボリュームおよび容量プールは、いっぱいになっても自動拡張されません。 「[Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)」を参照してください。   

容量ベースのアラート ルールを管理するには、コミュニティでサポートされている [Logic Apps ANFCapacityManager ツール](https://github.com/ANFTechTeam/ANFCapacityManager)を使用できます。 このツールでは、ボリュームの領域が不足しないように、ボリューム サイズを自動的に増やすことができます。

## <a name="does-the-destination-volume-of-a-replication-count-towards-hard-volume-quota"></a>レプリケーションの宛先ボリュームは、ハード ボリューム クォータにカウントされますか?  

いいえ。レプリケーションの宛先ボリュームは、ハード ボリューム クォータにカウントされません。

## <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Azure NetApp Files は Azure Storage Explorer を使用して管理できますか?

いいえ。 Azure NetApp Files は、Azure Storage Explorer ではサポートされていません。

## <a name="next-steps"></a>次のステップ  

- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [ネットワークに関する FAQ](faq-networking.md)
- [セキュリティに関する FAQ](faq-security.md)
- [パフォーマンスに関する FAQ](faq-performance.md)
- [NFS に関する FAQ](faq-nfs.md)
- [SMB に関する FAQ](faq-smb.md)
- [データの移行と保護に関する FAQ](faq-data-migration-protection.md)
- [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
- [アプリケーションの回復性に関する FAQ](faq-application-resilience.md)
- [統合に関する FAQ](faq-integration.md)
