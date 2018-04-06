---
title: Azure Storage のデータ レプリケーション | Microsoft Docs
description: Microsoft Azure Storage アカウント内のデータは、持続性と高可用性を保証するため、レプリケートされます。 レプリケーション オプションには、ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)、geo 冗長ストレージ (GRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS) などがあります。
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.openlocfilehash: 600b66af3b7da24c5a40d09d5cdf76f2d5be67ac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="azure-storage-replication"></a>Azure Storage のレプリケーション

Microsoft Azure ストレージ アカウント内のデータは、持続性と高可用性を保証するため、常にレプリケートされています。 レプリケーションは、一時的なハードウェア障害から保護するためにデータをコピーし、ご利用のアプリケーションのアップタイムを保持します。 

同じデータ センター内、同じリージョンのデータ センター間、またはリージョン間のいずれかを選択して、ご利用のデータをレプリケートできます。 また、複数のデータ センター間またはリージョン間でデータをレプリケートすると、1 か所で壊滅的な障害が発生してもデータは保護されます。

レプリケーションにより、障害が発生しても、ストレージ アカウントは[ストレージのサービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) を満たすことができます。 Azure Storage の持続性と可用性の保証については、SLA をご覧ください。

ストレージ アカウントを作成するときは、次のレプリケーション オプションのいずれかを選択できます。

* [ローカル冗長ストレージ (LRS)](#locally-redundant-storage)
* [ゾーン冗長ストレージ (ZRS)](#zone-redundant-storage)
* [geo 冗長ストレージ (GRS)](#geo-redundant-storage)
* [読み取りアクセス geo 冗長ストレージ (RA-GRS)](#read-access-geo-redundant-storage)

ローカル冗長ストレージ (LRS) は、ストレージ アカウントを作成するときの既定のオプションです。

次の表に、LRS、ZRS、GRS、RA-GRS の違いについて簡単な概要を示します。 この記事の後に続くセクションでは、各種のレプリケーションについて詳細に説明します。

| レプリケーションの方法 | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| 複数のデータセンター間でのデータのレプリケート |いいえ  |可能  |はい |[はい] |
| 1 次拠点に加えて 2 次拠点からもデータの読み取り可能 |いいえ  |いいえ  |いいえ  |[はい] |
| 1 年にわたってオブジェクトの持続性を提供するように設計 |99.999999999% (イレブン ナイン) 以上|99.9999999999% (トゥエルブ ナイン) 以上|99.99999999999999% (シックスティーン ナイン) 以上|99.99999999999999% (シックスティーン ナイン) 以上|

さまざまな冗長オプションの料金情報については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/) 」をご覧ください。

> [!NOTE]
> Premium Storage でサポートされるのは、ローカル冗長ストレージ (LRS) だけです。 Premium Storage については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../../virtual-machines/windows/premium-storage.md)」をご覧ください。
>

## <a name="locally-redundant-storage"></a>ローカル冗長ストレージ
[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

## <a name="zone-redundant-storage"></a>ゾーン冗長ストレージ
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="zrs-classic-accounts"></a>ZRS クラシック アカウント

既存の ZRS 機能は現在、ZRS クラシックと呼ばれています。 ZRS クラシック アカウントが利用できるのは、汎用 V1 ストレージ アカウントのブロック BLOB に限られるのでご注意ください。 

ZRS クラシックでは、1 つから 2 つのリージョン内の複数のデータセンターに、データが非同期的にレプリケートされます。 Microsoft がセカンダリへのフェールオーバーを開始していないと、レプリカを利用できないことがあります。 

ZRS クラシック アカウントと LRS、GRS、または RA-GRS を相互に変換することはできません。 また、ZRS クラシック アカウントは、メトリックまたはログをサポートしていません。   

1 つのリージョンで ZRS が一般公開されると、そのリージョンのポータルからは ZRS クラシック アカウントを作成できなくなりますが、他の方法で作成することはできます。  
ZRS クラシックから ZRS への自動移行プロセスは、今後提供される予定です。

ZRS アカウントのデータは、別のアカウント (LRS、ZRS クラシック、GRS、RAGRS) との間で、双方向に手動で移行することができます。 この手動での移行は、AzCopy、Azure Storage Explorer、Azure PowerShell、Azure CLI、または Azure Storage クライアント ライブラリの 1 つを使用して実行できます。

> [!NOTE]
> ZRS クラシック アカウントは、2021 年 3 月 31 日に非推奨になり、移行が必須になる予定です。 ZRS クラシックのお客様には、非推奨になる前に Microsoft から詳細をご連絡します。

ご不明な点があれば、「[よく寄せられる質問](#frequently-asked-questions)」セクションをご覧ください。 

## <a name="geo-redundant-storage"></a>geo 冗長ストレージ
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-GRS.md)]

## <a name="read-access-geo-redundant-storage"></a>読み取りアクセス geo 冗長ストレージ
読み取りアクセス geo 冗長ストレージ (RA-GRS) は、ストレージ アカウントの可用性を最大化します。 RA-GRS は、2 つのリージョンにまたがる geo レプリケーションだけでなく、セカンダリ ロケーションにあるデータに対する読み取り専用アクセスを提供します。

セカンダリ リージョンのデータに対して読み取り専用アクセスを有効にすると、ご利用のストレージ アカウントのプライマリ エンドポイントだけでなく、セカンダリ エンドポイントのデータも使用できます。 セカンダリ エンドポイントはプライマリ エンドポイントと似ていますが、アカウント名に `–secondary` というサフィックスが追加されます。 たとえば、Blob service のプライマリ エンドポイントが `myaccount.blob.core.windows.net` の場合、セカンダリ エンドポイントは `myaccount-secondary.blob.core.windows.net` になります。 ストレージ アカウントのアクセス キーは、プライマリ エンドポイントとセカンダリ エンドポイントの両方で同じです。

RA-GRS 使用時の考慮事項:

* RA-GRS を使うときは、通信対象のエンドポイントをアプリケーションで管理する必要があります。
* 非同期レプリケーションには遅延が伴うため、地域的な災害が発生した場合にデータをプライマリ リージョンから復旧できないと、セカンダリ リージョンにまだレプリケートされていない変更は失われる可能性があります。
* Microsoft がセカンダリ リージョンへのフェールオーバーを開始した場合、フェールオーバーの完了後、そのデータへの読み取り/書き込みアクセスができるようになります。 詳細については、[ディザスター リカバリーのガイダンス](../storage-disaster-recovery-guidance.md)をご覧ください。
* RA-GRS は高可用性を目的として作られています。 スケーラビリティのガイダンスについては、[パフォーマンス チェックリスト](../storage-performance-checklist.md)をご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

<a id="howtochange"></a>
#### <a name="1-how-can-i-change-the-geo-replication-type-of-my-storage-account"></a>1.ストレージ アカウントの geo レプリケーションの種類を変更するにはどうすればよいですか。

   ご利用のストレージ アカウントの geo レプリケーションの種類は、[Azure Portal](https://portal.azure.com/)、[Azure PowerShell](storage-powershell-guide-full.md)、または Azure Storage クライアント ライブラリの 1 つを使用して変更できます。

   > [!NOTE]
   > ZRS アカウントを LRS または GRS に変換することはできません。 同様に、既存の LRS または GRS アカウントを ZRS アカウントに変換することはできません。
    
<a id="changedowntime"></a>
#### <a name="2-does-changing-the-replication-type-of-my-storage-account-result-in-down-time"></a>2.使用しているストレージ アカウントのレプリケーションの種類を変更するとダウンタイムが発生しますか。

   いいえ。使用しているストレージ アカウントのレプリケーションの種類を変更してもダウンタイムは発生しません。

<a id="changecost"></a>
#### <a name="3-are-there-additional-costs-to-changing-the-replication-type-of-my-storage-account"></a>手順 3.使用しているストレージ アカウントのレプリケーションの種類を変更した場合、追加料金は発生しますか。

   はい。 ご利用のストレージ アカウントを LRS から GRS (または RA-GRS) に変更した場合、プライマリ ロケーションの既存のデータをセカンダリ ロケーションにコピーする際のエグレスに対して追加料金が発生します。 初回データ コピー後は、プライマリ ロケーションからセカンダリ ロケーションへの geo レプリケーションに関して、それ以上のエグレス料金が発生することはありません。 帯域幅の料金の詳細については、[Azure Storage の料金に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)をご覧ください。

   GRS から LRS に変更する場合、追加のコストは発生しませんが、セカンダリ ロケーションからデータが削除されます。

<a id="ragrsbenefits"></a>
#### <a name="4-how-can-ra-grs-help-me"></a>4.RA-GRS の利点を教えてください。

   GRS ストレージでは、プライマリ リージョンのデータが、数百マイル離れたセカンダリ リージョンにレプリケートされます。 GRS なら、地域的な停電やプライマリ リージョンで復旧できない災害が発生しても、データは保持されます。 RA-GRS ストレージでは、GRS レプリケーションに、セカンダリ ロケーションのデータを読み取る機能が追加されます。 高可用性を確保するためのお勧めの設計方法については、「[RA-GRS を使用した高可用性アプリケーションの設計](../storage-designing-ha-apps-with-ragrs.md)」をご覧ください。

<a id="lastsynctime"></a>
#### <a name="5-is-there-a-way-to-figure-out-how-long-it-takes-to-replicate-my-data-from-the-primary-to-the-secondary-region"></a>5.プライマリ リージョンからセカンダリ リージョンにデータをレプリケートするのにかかる時間を把握する方法はありますか。

   RA-GRS ストレージをご利用の場合、ストレージ アカウントの最後の同期時刻を確認できます。 最後の同期時刻は、GMT 日付/時刻の値です。 最後の同期時刻より前にプライマリに対して書き込まれたデータはすべて、セカンダリ ロケーションに対して正常に書き込まれています。つまり、これらのデータは、セカンダリ ロケーションから読み取ることができます。 最後の同期時刻より後にプライマリに対して書き込まれたデータに関しては、読み取りできる場合とできない場合とがあります。 この値を、[Azure Portal](https://portal.azure.com/)、[Azure PowerShell](storage-powershell-guide-full.md)、または Azure Storage クライアント ライブラリの 1 つを使用して、クエリできます。

<a id="outage"></a>
#### <a name="6-if-there-is-an-outage-in-the-primary-region-how-do-i-switch-to-the-secondary-region"></a>6.プライマリ リージョンで障害が発生した場合、セカンダリ リージョンに切り替えるにはどうすればよいですか。

   詳細については、「[Azure Storage の停止が発生した場合の対処方法](../storage-disaster-recovery-guidance.md)」をご覧ください。

<a id="rpo-rto"></a>
#### <a name="7-what-is-the-rpo-and-rto-with-grs"></a>7.GRS の RPO や RTO とは何ですか。

   **Recover Point Objective (RPO: 目標復旧ポイント):** GRS と RA-GRS のストレージ サービスでは、データがプライマリ ロケーションからセカンダリ ロケーションに非同期的に geo レプリケートされます。 プライマリ リージョンで大規模な地域災害が発生した場合、Microsoft はセカンダリ リージョンに対してフェールオーバーを実行します。 フェールオーバーが発生した場合、geo レプリケートされていない最近の変更が失われる可能性があります。 将来的に生じうるデータ消失までの時間 (分単位) を RPO といい、これはデータの復旧できる過去の時点を表します。 現在、geo レプリケーションの所要時間を規定する SLA はありませんが、Azure Storage では RPO を 15 分未満とするのが一般的です。

   **Recovery Time Objective (RTO: 目標復旧時間):** RTO は、フェールオーバーを実行してストレージ アカウントをオンラインに戻すまでにかかる時間の指標です。 フェールオーバーの所要時間には、次のアクションが含まれます。

   * プライマリ ロケーションでデータを復旧できるか、あるいはフェールオーバーが必要かどうかを、Microsoft が決定するまでに要する時間。
   * プライマリ DNS エントリの参照先をセカンダリ ロケーションに変更することで、ストレージ アカウントのフェールオーバーを実行する時間。

   Microsoft にはお客様のデータを保持する重大な責任があります。 プライマリ リージョンでデータを復旧できる可能性があれば、Microsoft はフェールオーバーを遅らせ、お客様のデータを復旧することに注力します。 このサービスの今後のバージョンでは、ご自身で RTO を制御できるよう、お客様がアカウント レベルでのフェールオーバーをトリガーできるようにする予定です。

#### <a name="8-what-azure-storage-objects-does-zrs-support"></a>8.ZRS はどのような Azure Storage オブジェクトをサポートしていますか。 
ブロック BLOB、ページ BLOB (これらのバッキング VM ディスクを除く)、テーブル、ファイル、キュー。 

#### <a name="9-does-zrs-also-include-geo-replication"></a>9.ZRS には geo レプリケーションも含まれていますか。 
現在、ZRS では geo レプリケーションはサポートされていません。 ディザスター リカバリーのためにリージョン間レプリケーションがシナリオに必要な場合は、代わりに GRS または RA-GRS ストレージ アカウントをご利用ください。   

#### <a name="10-what-happens-when-one-or-more-zrs-zones-go-down"></a>10.1 つ以上の ZRS ゾーンがダウンしたときはどうなりますか。 
1 番目のゾーンがダウンすると、引き続き ZRS はそのリージョンの残りの 2 つのゾーン間でお客様のデータのレプリカを書き込みます。 2 番目のゾーンがダウンすると、少なくとも 2 つのゾーンが再度使用できるようになるまで、読み書きのアクセスはできなくなります。 

## <a name="next-steps"></a>次の手順
* [RA-GRS ストレージを使用した高可用性アプリケーションの設計](../storage-designing-ha-apps-with-ragrs.md)
* [Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)
* [Azure ストレージ アカウントについて](../storage-create-storage-account.md)
* [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)
* [Microsoft Azure Storage 冗長オプションと読み取りアクセス geo 冗長ストレージ ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
* [SOSP ペーパー - Azure Storage: 強力な整合性を備えた高可用クラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
