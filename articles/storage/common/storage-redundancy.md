---
title: Azure Storage のデータ レプリケーション | Microsoft Docs
description: Microsoft Azure Storage アカウント内のデータは、持続性と高可用性を保証するため、レプリケートされます。 レプリケーション オプションには、ローカル冗長ストレージ (LRS)、ゾーン冗長ストレージ (ZRS)、geo 冗長ストレージ (GRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS) などがあります。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/21/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 18dfdce827a76d924494e66ceb0d03e2bb3a3ffe
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523762"
---
# <a name="azure-storage-replication"></a>Azure Storage のレプリケーション

Microsoft Azure ストレージ アカウント内のデータは、持続性と高可用性を保証するため、常にレプリケートされています。 Azure Storage のレプリケーションでは、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止または停電、大規模な自然災害など) からデータが保護されるようにデータがコピーされます。 同じデータ センター内、同じリージョンのゾーンのデータ センター間、さらにはリージョン間でデータをレプリケートすることもできます。

レプリケーションにより、障害が発生しても、ストレージ アカウントは[ストレージのサービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/storage/) を満たすことができます。 Azure Storage の持続性と可用性の保証については、SLA をご覧ください。

## <a name="choosing-a-replication-option"></a>レプリケーション オプションの選択

ストレージ アカウントを作成するときは、次のレプリケーション オプションのいずれかを選択できます。

* [ローカル冗長ストレージ (LRS)](storage-redundancy-lrs.md)
* [ゾーン冗長ストレージ (ZRS)](storage-redundancy-zrs.md)
* [geo 冗長ストレージ (GRS)](storage-redundancy-grs.md)
* [読み取りアクセス geo 冗長ストレージ (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

次の表は、特定の種類のイベント (または同様の影響を受けるイベント) に対して、各レプリケーション戦略が提供する持続性と可用性の範囲について概要を説明したものです。

| シナリオ                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| データ センター内でノードを使用できない                                                                 | [はい]                             | はい                              | はい                                  | [はい]                                  |
| データ センター全体 (ゾーンまたは非ゾーン) が使用できなくなる                                           | いいえ                               | 可能                               | はい                                  | [はい]                                  |
| リージョン全体の停止                                                                                     | いいえ                               | いいえ                                | 可能                                   | [はい]                                  |
| リージョン全体が使用できなくなった場合に (リモートの geo 冗長化されたリージョンの) データへの読み取りアクセス | いいえ                               | いいえ                                | いいえ                                    | [はい]                                  |
| 1 年にわたって ___ のオブジェクトの持続性を提供するように設計                                          | 99.999999999% (イレブン ナイン) 以上 | 99.9999999999% (トゥエルブ ナイン) 以上 | 99.99999999999999% (シックスティーン ナイン) 以上 | 99.99999999999999% (シックスティーン ナイン) 以上 |
| サポートされるストレージ アカウントの種類                                                                   | GPv1、GPv2、BLOB                | GPv2                             | GPv1、GPv2、BLOB                     | GPv1、GPv2、BLOB                     |

さまざまな冗長オプションの料金情報については、[Azure Storage の料金](https://azure.microsoft.com/pricing/details/storage/)に関するページを参照してください。

> [!NOTE]
> Premium Storage でサポートされるのは、ローカル冗長ストレージ (LRS) だけです。 Premium Storage については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../../virtual-machines/windows/premium-storage.md)」をご覧ください。

## <a name="changing-replication-strategy"></a>レプリケーション戦略の変更
[Azure Portal](https://portal.azure.com/)、[Azure Powershell](storage-powershell-guide-full.md)、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)、多くの [Azure クライアント ライブラリ](https://docs.microsoft.com/azure/index?view=azure-dotnet#pivot=sdkstools)のいずれかを使用して、ストレージ アカウントのレプリケーション戦略を変更できます。 使用しているストレージ アカウントのレプリケーションの種類を変更してもダウンタイムは発生しません。

   > [!NOTE]
   > 現在、Azure Portal または API を使用してアカウントを ZRS に変換することはできません。 アカウントのレプリケーションを ZRS に変換する場合は、[ゾーン冗長ストレージ (ZRS)](storage-redundancy-zrs.md) に関するページを参照してください。
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>アカウントのレプリケーション戦略を変更するためにコストはかかりますか
変換パスによって変わります。 最も冗長サービスのコストが低いものから高いサービスの順に並べると、LRS、ZRS、GRS、RA-GRS があります。 たとえば、LRS *から*何かに移行する場合、より高度な冗長レベルに移行することになるので、追加料金が発生します。 GRS または RA-GRS *に*移行する場合、(プライマリ リージョン内の) データがリモートのセカンダリ リージョンにレプリケートされているため、送信帯域幅の料金がかかります。 これは初期設定時に 1 回かかる料金です。 データがコピーされた後に、追加の変換料金はかかりません。 新しいデータや既存のデータへの更新をレプリケートする場合にのみ課金されます。 帯域幅の料金の詳細については、[Azure Storage の料金に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)をご覧ください。

GRS から LRS に変更する場合、追加のコストは発生しませんが、レプリケートされたデータはセカンダリ ロケーションから削除されます。

## <a name="see-also"></a>関連項目

- [ローカル冗長ストレージ (LRS): Azure Storage の低コストのデータ冗長性](storage-redundancy-lrs.md)
- [ゾーン冗長化ストレージ (ZRS): 高可用 Azure Storage アプリケーション](storage-redundancy-zrs.md)
- [geo 冗長ストレージ (GRS): Azure Storage のリージョン間レプリケーション](storage-redundancy-grs.md)
- [Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](storage-scalability-targets.md)
- [RA-GRS ストレージを使用した高可用性アプリケーションの設計](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage 冗長オプションと読み取りアクセス geo 冗長ストレージ ](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP ペーパー - Azure Storage: 強力な整合性を備えた高可用クラウド ストレージ サービス](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
