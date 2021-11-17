---
title: Azure NetApp Files のバックアップ の FAQ | Microsoft Docs
description: Azure NetApp Files のバックアップ機能に関するよくある質問 (FAQ) に答えます。
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
author: b-juche
ms.author: b-juche
ms.date: 10/11/2021
ms.openlocfilehash: cec399e00215994e55e06430f2170d8c19c20b03
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269913"
---
# <a name="azure-netapp-files-backup-faqs"></a>Azure NetApp Files のバックアップに関する FAQ

この記事では、[Azure NetApp Files のバックアップ](backup-introduction.md)機能に関するよくある質問 (FAQ) に答えます。 

## <a name="when-do-my-backups-occur"></a>バックアップはいつ発生しますか?   

Azure NetApp Files バックアップは、バックアップ ポリシーの頻度が入力された後、ランダム化された時間枠で開始されます。 たとえば、毎週のバックアップは、日曜日の午前 12 時以降のランダムに割り当てられた間隔で開始されます。 深夜。 このタイミングは、この時点でユーザーが変更することはできません。 ベースライン バックアップは、バックアップ ポリシーをボリュームに割り当てるとすぐに開始されます。

## <a name="what-happens-if-a-backup-operation-encounters-a-problem"></a>バックアップ操作で問題が発生した場合はどうなりますか?

バックアップ操作中に問題が発生した場合、Azure NetApp Files バックアップでは、ユーザーの操作を必要とせずに、操作が自動的に再試行されます。 再試行が引き続き失敗する場合は、Azure NetApp Files バックアップによって操作の失敗が報告されます。

## <a name="can-i-change-the-location-or-storage-tier-of-my-backup-vault"></a>バックアップ コンテナーの場所またはストレージ層を変更することはできますか?

いいえ。Azure NetApp Files では、Azure ストレージ上のバックアップ データ保存先を自動で管理します。 この保存先と Azure ストレージ層をユーザーが変更することはできません。

## <a name="what-types-of-security-are-provided-for-the-backup-data"></a>バックアップ データにはどのような種類のセキュリティが用意されていますか?

Azure NetApp Files は、受信したバックアップ データのエンコード時に AES-256 ビット暗号化を使用します。 さらに、暗号化されたデータは、HTTPS TLSv 1.2 接続を使用して Azure Storage に安全に送信されます。 Azure NetApp Files バックアップは、バックアップ データを格納するために、Azure Storage アカウントの組み込み暗号化の保存時機能に依存します。

## <a name="what-happens-to-the-backups-when-i-delete-a-volume-or-my-netapp-account"></a>ボリュームまたは NetApp アカウントを削除した場合、バックアップはどうなりますか? 

 Azure NetApp Files ボリュームを削除したときに、バックアップは保持されます。 バックアップを保持しない場合は、ボリュームを削除する前にバックアップを無効にします。 NetApp アカウントを削除しても、そのバックアップは同じサブスクリプションの他の NetApp アカウントに保持されて表示されるため、引き続き復元に使用できます。 サブスクリプションのすべての NetApp アカウントを削除する場合は、すべての NetApp アカウントのすべてのボリュームを削除する前に、バックアップを無効にしておく必要があります。

## <a name="whats-the-systems-maximum-backup-retries-for-a-volume"></a>1 つのボリュームに対するシステムの最大バックアップ再試行回数は何回ですか?  

システムでは、スケジュールされたバックアップ ジョブを処理するとき、10 回まで再試行します。 ジョブが失敗した場合、システムはバックアップ操作に失敗します。 (構成済みのポリシーに基づいて) スケジュールされたバックアップの場合、システムは 1 時間ごとにデータのバックアップを試行します。 転送されなかった (または前回の試行中に失敗した) 新しいスナップショットが使用可能な場合、それらのスナップショットは転送対象と見なされます。 

## <a name="next-steps"></a>次のステップ  

- [Azure サポート要求を作成する方法](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [ネットワークに関する FAQ](faq-networking.md)
- [セキュリティに関する FAQ](faq-security.md)
- [パフォーマンスに関する FAQ](faq-performance.md)
- [NFS に関する FAQ](faq-nfs.md)
- [SMB に関する FAQ](faq-smb.md)
- [容量管理に関する FAQ](faq-capacity-management.md)
- [データの移行と保護に関する FAQ](faq-data-migration-protection.md)
- [アプリケーションの回復性に関する FAQ](faq-application-resilience.md)
- [統合に関する FAQ](faq-integration.md)
