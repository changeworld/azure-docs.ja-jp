---
title: Azure NetApp Files バックアップについて | Microsoft Docs
description: Azure NetApp Files バックアップの機能、サポートされているリージョン、コスト モデルについて説明します。
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
ms.date: 10/13/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: 3836dc37a6be1b21a46424cbcf14a8960908969d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256326"
---
# <a name="understand-azure-netapp-files-backup"></a>Azure NetApp Files バックアップについて

Azure NetApp Files バックアップでは、長期的な回復、アーカイブ、コンプライアンスのためのフル マネージド バックアップ ソリューションを提供することで、Azure NetApp Files のデータ保護機能が拡張されます。 サービスによって作成されたバックアップは、短期的な回復や複製に使用できるボリューム スナップショットに依存せずに、Azure ストレージに格納されます。 サービスによって作成されたバックアップは、リージョン内の新しい Azure NetApp Files ボリュームに復元できます。 Azure NetApp Files バックアップでは、ポリシーベース (スケジュールされた) バックアップと手動 (オンデマンド) バックアップの両方がサポートされます。 詳細については、「[Azure NetApp Files のスナップショットのしくみ](snapshots-introduction.md)」を参照してください。

> [!IMPORTANT]
> Azure NetApp Files バックアップ機能は、現在プレビューの段階です。 機能にアクセスするための順番待ちリクエストを、「 **[Azure NetApp Files バックアップ パブリック プレビュー](https://aka.ms/anfbackuppreviewsignup)** 」ページから送信する必要があります。 Azure NetApp Files チームからの正式な確認メールを待ってから、Azure NetApp Files バックアップ機能を使用してください。

## <a name="supported-regions"></a>サポートされているリージョン 

Azure NetApp Files バックアップは、次のリージョンでサポートされています。   

* 米国東部
* 米国東部 2
* 米国西部 
* 米国中南部
* 米国西部 2
* 北ヨーロッパ 
* 西ヨーロッパ
* オーストラリア東部
* 東日本

## <a name="cost-model-for-azure-netapp-files-backup"></a>Azure NetApp Files バックアップのコスト モデル

Azure NetApp Files バックアップの価格は、バックアップで使用されているストレージの総量に基づきます。 セットアップ料金や最小使用料金はありません。 バックアップ復元の価格は、請求サイクル中に復元されたバックアップ容量の合計に基づいて決定されます。

価格の例として、次のような状況を考えてください。

* ソース ボリュームは Azure NetApp Files Premium サービス レベルのものです。 ボリュームのクォータ サイズは 1000 GiB で、ボリュームは月の初日の始めに 500 GiB のサイズが消費されます。 このボリュームは米国中南部リージョンにあります。
* 5 つのローカル スナップショットを保持する日次の "*スナップショット*" ポリシーと、30 個のバックアップ コピーを保持する日次の "*バックアップ*" ポリシーを構成しました。
* わかりやすくするために、ソース ボリュームでは毎日データが一定で 1% 変化するが、消費される総ボリュームのサイズは増加しない (500 GiB のまま) であると仮定します。

バックアップ ポリシーがボリュームに割り当てられると、サービス管理の Azure ストレージへのベースライン バックアップが開始されます。 バックアップが完了すると、500 GiB のベースライン バックアップがボリュームのバックアップ リストに追加されます。 ベースライン転送後、日次バックアップでは変更されたブロックのみがバックアップされます。 毎日 5 GiB の増分バックアップが追加されるとすると、使用されるバックアップ ストレージの合計は `500GiB + 30*5GiB = 650GiB` になります。

バックアップに対する請求は、バックアップで使用されているストレージの合計容量に対して 1 か月あたり $0.05 のレートで月末に行われます。  つまり、毎月のバックアップの合計が 650 GiB の場合、料金は `650*$0.05=$32.5` になります。 通常の Azure NetApp Files ストレージ容量は、ローカル スナップショットに適用されます。 詳細については、「[Azure NetApp Files の価格](https://azure.microsoft.com/pricing/details/netapp/)」ページを参照してください。

たとえば 600 GiB のバックアップを新しいボリュームに復元することを選択した場合、バックアップ容量復元には GiB あたり $0.02 のレートで課金されます。 この場合、復元操作では `600*$0.02 = $12` になります。 

## <a name="next-steps"></a>次のステップ

* [Azure NetApp Files の要件と考慮事項](backup-requirements-considerations.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [ポリシー ベースのバックアップの構成](backup-configure-policy-based.md)
* [手動バックアップの構成](backup-configure-manual.md)
* [バックアップ ポリシーを管理する](backup-manage-policies.md)
* [バックアップを検索する](backup-search.md)
* [バックアップを新しいボリュームに復元する](backup-restore-new-volume.md)
* [ボリュームのバックアップ機能を無効にする](backup-disable.md)
* [ボリュームのバックアップを削除する](backup-delete.md)
* [ボリュームのバックアップ メトリック](azure-netapp-files-metrics.md#volume-backup-metrics)
* [Azure NetApp Files のバックアップに関する FAQ](faq-backup.md)
* [Azure NetApp Files のスナップショットのしくみ](snapshots-introduction.md)
