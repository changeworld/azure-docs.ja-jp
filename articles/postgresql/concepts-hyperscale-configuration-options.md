---
title: パフォーマンス オプション – Hyperscale (Citus) - Azure Database for PostgreSQL
description: ノードのコンピューティング、ストレージ、およびリージョンを含む、Hyperscale (Citus) サーバー グループのオプションです。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 2ee3e661d6c01aa2e4f37ac9a70e00be5da5f794
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975637"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database for PostgreSQL – Hyperscale (Citus) パフォーマンス オプション

## <a name="compute-and-storage"></a>コンピューティングとストレージ
 
Hyperscale (Citus) サーバー グループ内のワーカー ノードおよびコーディネーター ノードのコンピューティングおよびストレージ設定を別々に選択できます。  コンピューティング リソースは仮想コアとして提供されます。仮想コアは、基礎となるハードウェアの論理 CPU を表します。 プロビジョニングのストレージ サイズは、Hyperscale (Citus) サーバー グループのコーディネーターおよびワーカー ノードに利用できる容量を参照します。 ストレージには、データベース ファイル、一時ファイル、トランザクション ログ、および Postgres サーバー ログが含まれます。 プロビジョニングするストレージの合計容量によって、ワーカーおよびコーディネーター ノードそれぞれに利用できる I/O 容量も決まります。
 
|                       | ワーカー ノード           | コーディネーター ノード      |
|-----------------------|-----------------------|-----------------------|
| コンピューティング、仮想コア       | 4、8、16、32、64      | 4、8、16、32、64      |
| 仮想コアあたりのメモリ、GiB | 8                     | 4                     |
| ストレージ サイズ、TiB     | 0.5、1、2             | 0.5、1、2             |
| ストレージの種類          | 汎用 (SSD) | 汎用 (SSD) |
| IOPS                  | 最大 3 IOPS/GiB      | 最大 3 IOPS/GiB      |


## <a name="regions"></a>リージョン
Hyperscale (Citus) サーバー グループは次の Azure リージョンで使用できます。

* アメリカ合衆国:
    * カナダ中部 *
    * East US
    * 米国東部 2
    * 米国中北部 *
    * 米国西部 2
* アジア太平洋:
    * オーストラリア東部 *
    * 東南アジア
* ヨーロッパ:
    * 北ヨーロッパ
    * 英国南部
    * 西ヨーロッパ

アスタリスク (\*) が付いているリージョンでは、[高可用性](concepts-hyperscale-high-availability.md)はまだサポートされていません。

## <a name="pricing"></a>価格
最新の料金情報については、サービスの[料金ページ](https://azure.microsoft.com/pricing/details/postgresql/)を参照してください。
必要な構成のコストについては、[Microsoft Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) で、選択したオプションに基づいて表示される **[構成]** タブの月額コストを確認します。 Azure サブスクリプションを取得していない場合は、Azure 料金計算ツールを使用して見積もり価格を確認できます。 [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)の Web サイトで **[項目の追加]** を選択し、 **[データベース]** カテゴリを展開し、 **[Azure Database for PostgreSQL – Hyperscale (Citus)]** を選択してオプションをカスタマイズします。
 
## <a name="next-steps"></a>次の手順
[ポータルで Hyperscale (Citus) サーバー グループを作成する](quickstart-create-hyperscale-portal.md)方法について学習します。
