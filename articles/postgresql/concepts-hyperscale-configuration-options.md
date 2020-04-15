---
title: 構成オプション - Hyperscale (Citus) - Azure Database for PostgreSQL
description: ノードのコンピューティング、ストレージ、およびリージョンを含む、Hyperscale (Citus) サーバー グループのオプションです。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804590"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL - Hyperscale (Citus) の構成オプション

## <a name="compute-and-storage"></a>コンピューティングとストレージ
 
Hyperscale (Citus) サーバー グループ内のワーカー ノードおよびコーディネーター ノードのコンピューティングおよびストレージ設定を別々に選択できます。  コンピューティング リソースは仮想コアとして提供されます。仮想コアは、基礎となるハードウェアの論理 CPU を表します。 プロビジョニングのストレージ サイズは、Hyperscale (Citus) サーバー グループのコーディネーターおよびワーカー ノードに利用できる容量を参照します。 ストレージには、データベース ファイル、一時ファイル、トランザクション ログ、および Postgres サーバー ログが含まれます。
 
|                       | ワーカー ノード           | コーディネーター ノード      |
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

## <a name="regions"></a>リージョン
Hyperscale (Citus) サーバー グループは次の Azure リージョンで使用できます。

* アメリカ合衆国:
    * カナダ中部
    * 米国中部
    * 米国東部
    * 米国東部 2
    * 米国中北部
    * 米国西部 2
* アジア太平洋:
    * オーストラリア東部
    * 東日本
    * 韓国中部
    * 東南アジア
* ヨーロッパ:
    * 北ヨーロッパ
    * 英国南部
    * 西ヨーロッパ

これらのリージョンの一部は、Azure サブスクリプションによっては最初はアクティブ化されていない可能性があります。 上の一覧のリージョンを使用したいが、サブスクリプションに表示されない場合、またはこの一覧にないリージョンを使用したい場合は、[サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いてください。

## <a name="pricing"></a>価格
最新の料金情報については、サービスの[料金ページ](https://azure.microsoft.com/pricing/details/postgresql/)を参照してください。
必要な構成のコストについては、[Microsoft Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) で、選択したオプションに基づいて表示される **[構成]** タブの月額コストを確認します。 Azure サブスクリプションを取得していない場合は、Azure 料金計算ツールを使用して見積もり価格を確認できます。 [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)の Web サイトで **[項目の追加]** を選択し、 **[データベース]** カテゴリを展開し、 **[Azure Database for PostgreSQL – Hyperscale (Citus)]** を選択してオプションをカスタマイズします。
 
## <a name="next-steps"></a>次のステップ
[ポータルで Hyperscale (Citus) サーバー グループを作成する](quickstart-create-hyperscale-portal.md)方法について学習します。
