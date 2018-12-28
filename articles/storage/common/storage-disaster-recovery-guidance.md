---
title: Azure Storage の停止が発生した場合の対処方法 | Microsoft Docs
description: Azure Storage の停止が発生した場合の対処方法
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 12/12/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 39a938d45c8f15c21b44bb5b04b1429fb4733b5a
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323270"
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>Azure Storage の停止が発生した場合の対処方法
Microsoft では、サービスがいつでも使用できるように取り組んでいますが、 やむを得ない事情により、計画されていないサービス停止が 1 つまたは複数のリージョンで発生することがあります。 こうした状況はほとんど発生しませんが、発生した場合は、次のガイダンスに従って対応してください。

## <a name="how-to-prepare"></a>準備する方法
すべての顧客が独自のディザスター リカバリー計画を準備することが重要です。 ストレージの停止から復旧し、アプリケーションをアクティブ化して、機能している状態に戻すには、通常、運用担当者の操作と自動処理の両方が必要です。 ディザスター リカバリー計画を作成するには、次の Azure ドキュメントをご覧ください。

* [可用性のチェックリスト](https://docs.microsoft.com/azure/architecture/checklist/availability)
* [回復性に優れた Azure 用アプリケーションの設計](https://docs.microsoft.com/azure/architecture/resiliency/)
* [Azure Site Recovery サービス](https://azure.microsoft.com/services/site-recovery/)
* [Azure Storage のレプリケーション](https://docs.microsoft.com/azure/storage/common/storage-redundancy)
* [Azure Backup サービス](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>検出する方法
Azure サービスの状態は、 [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)をサブスクライブして確認することをお勧めします。

## <a name="what-to-do-if-a-storage-outage-occurs"></a>Storage の停止が発生した場合の対処方法
1 つ以上のリージョンで 1 つ以上の Storage サービスが一時的に使用できない場合は、2 つのオプションがあります。 すぐにデータにアクセスする必要がある場合は、オプション 2 を検討してください。

### <a name="option-1-wait-for-recovery"></a>オプション 1:復旧を待つ
この場合、ユーザーによる操作は必要ありません。 Azure サービスを利用できるようにするために鋭意取り組んでいます。 サービスの状態は [Azure サービス正常性ダッシュボード](https://azure.microsoft.com/status/)で監視できます。

### <a name="option-2-copy-data-from-secondary"></a>オプション 2:セカンダリ リージョンからデータをコピーする
ストレージ アカウントに対して [読み取りアクセス geo 冗長ストレージ (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (推奨) を選択した場合は、セカンダリ リージョンからデータに読み取りアクセスできます。 [AzCopy](storage-use-azcopy.md)、[Azure PowerShell](storage-powershell-guide-full.md)、[Azure Data Movement Library](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) などのツールを使用して、セカンダリ リージョンから、影響を受けていないリージョンの別のストレージ アカウントにデータをコピーし、読み取りと書き込みの両方の可用性について、アプリケーションがそのストレージ アカウントを指すように指定します。

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>Storage のフェールオーバーが発生した場合
[Geo 冗長ストレージ (GRS)](storage-redundancy-grs.md) または[読み取りアクセス geo 冗長ストレージ (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage) (推奨) を選択した場合、Azure Storage では 2 つのリージョン (プライマリおよびセカンダリ) でデータの持続性が維持されます。 この両方のリージョンでは、データのレプリカが常に複数保持されています。

地域的な災害がプライマリ リージョンに影響する場合、Microsoft では、まず、そのリージョン内のサービスを復元して、RTO と RPO の最適な組み合わせを提供しようとします。 災害の性質とその影響によっては、まれではありますが、プライマリ リージョンを復元できないことがあります。 その場合は geo フェールオーバーを実行します。 リージョン間のデータ レプリケーションは非同期プロセスのため、遅延が発生し、セカンダリ リージョンにレプリケートされていない変更は失われる可能性があります。

ストレージ geo フェールオーバーに関する注意事項をいくつか次に示します。

* ストレージ geo フェールオーバーは、Azure Storage チームによってのみトリガーされます。顧客の操作は不要です。 フェールオーバーは、Azure Storage チームが、同じリージョンにデータを復元するすべてのオプションを使い果たしたときにトリガーされ、これにより RTO と RPO の最適な組み合わせが提供されます。
* BLOB、テーブル、キュー、およびファイルに対する既存のストレージ サービス エンドポイントは、フェールオーバー後も同じままです。プライマリ リージョンからセカンダリ リージョンに切り替えるには、Microsoft 提供の DNS エントリを更新する必要があります。 Microsoft は、geo フェールオーバー プロセスの一環として自動的にこの更新を実行します。
* geo フェールオーバー前と geo フェールオーバー中は、障害の影響によりストレージ アカウントに書き込みアクセスできません。ただし、ストレージ アカウントが RA-GRS として構成されている場合、セカンダリ リージョンからの読み取りは引き続き可能です。
* geo フェールオーバーが完了し、DNS の変更が反映されたとき、GRS または RA-GRS があると、お使いのストレージ アカウントへの読み取り/書き込みアクセスが復元されます。 以前セカンダリ エンドポイントだったエンドポイントが、プライマリ エンドポイントになります。 
* プライマリの場所の状態を確認し、お使いのストレージ アカウントに対する最終 geo フェールオーバー時刻を照会できます。 詳細については、「[Storage Accounts - Get Properties (ストレージ アカウント - プロパティの取得)](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/getproperties)」を参照してください。
* フェールオーバー後、ストレージ アカウントは完全に機能しますが、"低下" 状態になります。これは geo レプリケーションを利用できないスタンドアロン リージョンでホストされているためです。 このリスクを軽減するために、Microsoft では、元のプライマリ リージョンを復元してから、geo フェールバックを実行し、元の状態を復元します。 元のプライマリ リージョンが復旧できない場合は、別のセカンダリ リージョンを割り当てます。

## <a name="best-practices-for-protecting-your-data"></a>データ保護のベスト プラクティス
ストレージ データを定期的にバックアップするための推奨アプローチがいくつかあります。

* VM ディスク - [Azure Backup サービス](https://azure.microsoft.com/services/backup/) を使用して、Azure 仮想マシンで使用される VM ディスクをバックアップします。
* ブロック BLOB - [ソフト削除](../blobs/storage-blob-soft-delete.md)を有効にしてオブジェクトレベルの削除および上書きから保護するか、[AzCopy](storage-use-azcopy.md)、[Azure PowerShell](storage-powershell-guide-full.md)、または [Azure Data Movement Library](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) を使用して、他のリージョンの別のストレージ アカウントに BLOB をコピーします。
* テーブル - [AzCopy](storage-use-azcopy.md) を使用して、テーブル データを、他のリージョンの別のストレージ アカウントにエクスポートします。
* ファイル - [AzCopy](storage-use-azcopy.md) または [Azure PowerShell](storage-powershell-guide-full.md) を使用して、他のリージョンの別のストレージ アカウントにファイルをコピーします。

RA-GRS の機能を最大限に活用したアプリケーションを設計する方法の詳細については、「[RA-GRS ストレージを使用した高可用性アプリケーションの設計](../storage-designing-ha-apps-with-ragrs.md)」を参照してください。
