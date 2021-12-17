---
title: Azure NetApp Files リージョン間レプリケーションのための要件と考慮事項 | Microsoft Docs
description: Azure NetApp Files ボリュームのリージョン間レプリケーション機能を使用するための要件と考慮事項について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: b-juche
ms.openlocfilehash: b098243c3f4cf160aadfb2fa001c23c4cad691bd
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042391"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>リージョン間レプリケーションを使用するための要件と考慮事項 

この記事では、Azure NetApp Files の[ボリュームのリージョン間レプリケーション機能の使用](cross-region-replication-create-peering.md)に関する要件と考慮事項について説明します。

## <a name="requirements-and-considerations"></a>要件と考慮事項 

* Azure NetApp Files レプリケーションは、特定の固定されたリージョン ペアでのみ使用できます。 「[サポートされているリージョン ペア](cross-region-replication-introduction.md#supported-region-pairs)」を参照してください。 
* SMB ボリュームは、NFS ボリュームと共にサポートされます。 SMB ボリュームのレプリケーションを行うには、ソースと宛先の NetApp アカウントに Active Directory 接続が必要です。 宛先の AD 接続には、宛先リージョン内の委任されたサブネットから到達可能な DNS サーバーまたは ADDS ドメイン コントローラーへのアクセス権が含まれている必要があります。 詳細については、「[Active Directory 接続の要件](create-active-directory-connections.md#requirements-for-active-directory-connections)」を参照してください。 
* 宛先アカウントは、ソース ボリューム リージョンとは異なるリージョンにある必要があります。 別のリージョン内の既存の NetApp アカウントを選択することもできます。  
* [宛先リージョンにフェールオーバー](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume)して、宛先ボリュームで読み取りと書き込みを有効にするまで、レプリケーションの宛先ボリュームは読み取り専用になります。 
* 現在、Azure NetApp Files レプリケーションでは複数のサブスクリプションがサポートされていません。レプリケーションはすべて、1 つのサブスクリプションで実行する必要があります。
* リージョン間レプリケーションの宛先ボリュームの最大数については、[リソース制限](azure-netapp-files-resource-limits.md)に関する記事を参照してください。 サポート チケットを開くことで、レプリケーション宛先ボリュームの既定のクォータ (リージョン内のサブスクリプションあたり) の[制限増大を要求する](azure-netapp-files-resource-limits.md#request-limit-increase)ことができます。
* ソース ボリュームに新しく追加されたスナップショットがインターフェイスに反映されるまでに、最大 5 分の遅延が発生する場合があります。  
* カスケード、ファン イン、ファン アウト トポロジはサポートされていません。
* 現時点では、スナップショットから作成されたソース ボリュームについてボリューム レプリケーションを構成することはサポートされていません。
* リージョン間レプリケーションを設定したら、ソース ボリュームと宛先ボリューム間に参照を提供するための "*snapmirror スナップショット*" がレプリケーション プロセスによって作成されます。 snapmirror スナップショットは、増分転送ごとに新しいスナップショットが作成されると、毎回自動的に循環されます。 レプリケーション リレーションシップとボリュームが削除されるまで、snapmirror スナップショットを削除することはできません。 
* レプリケーション リレーションシップがアクティブであるかまたは切断されている場合、またレプリケーション リレーションシップが削除された後でも、レプリケーション リレーションシップのソース ボリューム上の手動スナップショットを削除できます。 レプリケーション リレーションシップが切断されるまで、宛先ボリュームの手動スナップショットを削除することはできません。
* リージョン間レプリケーションのソース ボリュームまたは宛先ボリュームをスナップショットに戻すことはできません。 レプリケーション関係のボリュームでは、スナップショットの元に戻す機能がグレーで表示されます。 

## <a name="next-steps"></a>次のステップ
* [ボリューム レプリケーションを作成する](cross-region-replication-create-peering.md)
* [レプリケーション関係の正常性状態を表示する](cross-region-replication-display-health-status.md)
* [ディザスター リカバリーの管理](cross-region-replication-manage-disaster-recovery.md)
* [ボリューム レプリケーション メトリック](azure-netapp-files-metrics.md#replication)
* [ボリューム レプリケーションまたはボリュームを削除する](cross-region-replication-delete.md)
* [リージョン間レプリケーションのトラブルシューティング](troubleshoot-cross-region-replication.md)


