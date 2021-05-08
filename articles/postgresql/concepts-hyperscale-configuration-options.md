---
title: 構成オプション - Hyperscale (Citus) - Azure Database for PostgreSQL
description: ノードのコンピューティング、ストレージ、およびリージョンを含む、Hyperscale (Citus) サーバー グループのオプションです。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/07/2021
ms.openlocfilehash: 1dd0666c2946896ed324fb3986bb7946890b73de
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388705"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL - Hyperscale (Citus) の構成オプション

## <a name="compute-and-storage"></a>コンピューティングとストレージ
 
Hyperscale (Citus) サーバー グループ内のワーカー ノードおよびコーディネーター ノードのコンピューティングおよびストレージ設定を別々に選択できます。  コンピューティング リソースは仮想コアとして提供されます。仮想コアは、基礎となるハードウェアの論理 CPU を表します。 プロビジョニングのストレージ サイズは、Hyperscale (Citus) サーバー グループのコーディネーターおよびワーカー ノードに利用できる容量を参照します。 ストレージには、データベース ファイル、一時ファイル、トランザクション ログ、および Postgres サーバー ログが含まれます。

### <a name="standard-tier"></a>Standard レベル
 
| リソース              | ワーカー ノード           | コーディネーター ノード      |
|-----------------------|-----------------------|-----------------------|
| コンピューティング、仮想コア       | 4、8、16、32、64      | 4、8、16、32、64      |
| 仮想コアあたりのメモリ、GiB | 8                     | 4                     |
| ストレージ サイズ、TiB     | 0.5、1、2             | 0.5、1、2             |
| ストレージの種類          | 汎用 (SSD) | 汎用 (SSD) |
| IOPS                  | 最大 3 IOPS/GiB      | 最大 3 IOPS/GiB      |

1 つの Hyperscale (Citus) ノードの RAM の合計容量は、選択した仮想コアの数に基づいています。

| 仮想コア | 1 つのワーカー ノード、GiB RAM | コーディネーター ノード、GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

プロビジョニングするストレージの合計容量によって、ワーカーおよびコーディネーター ノードそれぞれに利用できる I/O 容量も決まります。

| ストレージ サイズ、TiB | 最大 IOPS |
|-------------------|--------------|
| 0.5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6,148        |

Hyperscale (Citus) クラスター全体で集計された IOPS は次の値になります。

| ワーカー ノード | 0.5 TiB、合計 IOPS | 1 TiB、合計 IOPS | 2 TiB、合計 IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6,144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

### <a name="basic-tier-preview"></a>Basic レベル (プレビュー)

> [!IMPORTANT]
> Hyperscale (Citus) Basic レベルは現在プレビュー段階です。  このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
>
> その他の新機能については、[Hyperscale (Citus) のプレビュー機能](hyperscale-preview-features.md)に関するページで全一覧をご覧いただけます。

Hyperscale (Citus) [Basic レベル](concepts-hyperscale-tiers.md)は、ノードが 1 つだけのサーバー グループです。  コーディネーターとワーカー ノードは区別されないため、コンピューティングとストレージのリソースを選択するのはそれほど複雑ではありません。

| リソース              | 使用可能なオプション     |
|-----------------------|-----------------------|
| コンピューティング、仮想コア       | 2、4、8               |
| 仮想コアあたりのメモリ、GiB | 4                     |
| ストレージ サイズ、GiB     | 128、256、512         |
| ストレージの種類          | 汎用 (SSD) |
| IOPS                  | 最大 3 IOPS/GiB      |

1 つの Hyperscale (Citus) ノードの RAM の合計容量は、選択した仮想コアの数に基づいています。

| 仮想コア | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

プロビジョニングするストレージの合計容量によって、Basic レベルのノードで使用できる I/O 容量も決まります。

| ストレージ サイズ、GiB | 最大 IOPS |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1,536        |

## <a name="regions"></a>リージョン
Hyperscale (Citus) サーバー グループは次の Azure リージョンで使用できます。

* アメリカ合衆国:
    * ブラジル南部
    * カナダ中部
    * 米国中部
    * 米国東部 *
    * 米国東部 2
    * 米国中北部
    * 米国西部 2
* アジア太平洋:
    * オーストラリア東部
    * 東日本
    * 韓国中部
    * 東南アジア
* ヨーロッパ:
    * フランス中部
    * 北ヨーロッパ
    * 英国南部
    * 西ヨーロッパ

(\* = [プレビュー機能](hyperscale-preview-features.md)をサポートしています)

これらのリージョンの一部は、Azure サブスクリプションによっては最初はアクティブ化されていない可能性があります。 上の一覧のリージョンを使用したいが、サブスクリプションに表示されない場合、またはこの一覧にないリージョンを使用したい場合は、[サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いてください。

## <a name="pricing"></a>価格
最新の料金情報については、サービスの[料金ページ](https://azure.microsoft.com/pricing/details/postgresql/)を参照してください。
必要な構成のコストについては、[Microsoft Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) で、選択したオプションに基づいて表示される **[構成]** タブの月額コストを確認します。 Azure サブスクリプションを取得していない場合は、Azure 料金計算ツールを使用して見積もり価格を確認できます。 [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)の Web サイトで **[項目の追加]** を選択し、 **[データベース]** カテゴリを展開し、 **[Azure Database for PostgreSQL – Hyperscale (Citus)]** を選択してオプションをカスタマイズします。
 
## <a name="next-steps"></a>次のステップ
[ポータルで Hyperscale (Citus) サーバー グループを作成する](quickstart-create-hyperscale-portal.md)方法について学習します。
