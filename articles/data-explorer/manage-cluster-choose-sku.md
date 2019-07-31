---
title: Azure Data Explorer クラスターに適した VM SKU を選択する
description: この記事では、Azure Data Explorer クラスターに最適な SKU サイズを選択する方法について説明します。
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 0239111ca56dfe431a00eee83c79eedccc66c927
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226147"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Azure Data Explorer クラスターに適した VM SKU を選択する 

Azure Data Explorer には、新しいクラスターを作成するとき、または変化するワークロードに対してクラスターを最適化するときに選択できる、複数の VM SKU があります。 VM は、すべてのワークロードに最適なコストになるように慎重に選択されました。 

データ管理クラスターのサイズと VM SKU は、Azure Data Explorer サービスによって完全に管理されます。 エンジンの VM サイズやインジェスト ワークロードなどの要因によって決定されます。 

エンジン クラスターの VM SKU は、[クラスターをスケールアップする](manage-cluster-vertical-scaling.md)ことによりいつでも変更できます。 そのため、初期シナリオに適した最小の SKU サイズから始めるのが最善の方法です。 クラスターをスケールアップすると、新しい VM SKU でクラスターが再作成される間、最大 30 分のダウンタイムが発生することに注意してください。

> [!TIP]
> コンピューティング [RI (予約インスタンス)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) は、Azure Data Explorer クラスターに適用されます。  

この記事では、さまざまな VM SKU オプションについて説明し、最善の選択を行うのに役立つ技術的な詳細情報を提供します。

## <a name="select-the-cluster-type"></a>クラスターの種類を選択する

Azure Data Explorer には、次の 2 種類のクラスターが用意されています。

* **運用**: 運用クラスターには、エンジン クラスター用とデータ管理クラスター用の 2 つのノードが含まれており、Azure Data Explorer の [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/) で運用されます。

* **Dev/Test (SLA なし)** : Dev/Test クラスターには、エンジン クラスター用の 1 つの D11_v2 ノードと、データ管理クラスター用の 1 つの D1 ノードがあります。 このクラスターの種類は、インスタンス数が少なく、エンジン マークアップ料金がないため、最も低コストの構成です。 冗長性がないため、このクラスター構成には SLA はありません。

## <a name="sku-types"></a>SKU の種類

Azure Data Explorer クラスターを作成するときに、計画されたワークロードに "*最適な*" VM SKU を選択します。 Azure Data Explorer では、次の 2 つの SKU ファミリから選択できます。

* **D_V2**: **D** SKU は、コンピューティングに最適化されており、2 つのフレーバーで提供されます。
    * VM 自体
    * Premium Storage ディスクにバンドルされた VM

* **LS**: **L** SKU は、ストレージに最適化されています。 同程度の価格の **D** SKU より、SSD サイズがかなり大きくなります。

次の表では、使用可能な SKU の種類の主な違いを示します。
 
|**属性** | **D SKU** | **L SKU**
|---|---|---
|**小規模な SKU**|最小サイズは 2 コアの "D11" です|最小サイズは 4 コアの "L4" です
|**可用性**|すべてのリージョンで利用できます (DS+PS バージョンの可用性はさらに制限されます)|一部のリージョンで利用できます
|**コアあたりで GB キャッシュあたりのコスト**|D SKU では高く、DS+PS バージョンでは低くなります|"*従量課金制*" オプションが最低コスト
|**RI (予約インスタンス) の価格**|高割引 (3 年のコミットメントで 55% 以上)|低割引 (3 年のコミットメントで 20%)  

## <a name="select-your-cluster-vm"></a>クラスターの VM を選択する 

クラスター VM を選択するには、[垂直方向のスケーリングを構成します](manage-cluster-vertical-scaling.md#configure-vertical-scaling)。 

さまざまな VM SKU オプションを使用し、目的のシナリオに必要なパフォーマンスとホット キャッシュの要件に応じてコストを最適化することができます。 クエリの量が多い場合に最適なパフォーマンスが求められるシナリオの場合、理想的な SKU はコンピューティングに最適化されている必要があります。 一方、比較的低いクエリ負荷で大量のデータのクエリを実行する必要があるシナリオでは、ストレージに最適化された SKU を使用すると、コストを削減しながら、優れたパフォーマンスも得られます。

小規模な SKU ではクラスターあたりのインスタンス数が制限されているため、より大きい RAM を備えた大きい VM を使用することをお勧めします。 RAM のサイズは、`joins` を使用するクエリなど、必要な RAM リソースが多い一部の種類のクエリに必要です。 したがって、スケーリング オプションを検討するときは、インスタンスを追加してスケールアウトより、大きな SKU にスケールアップすることをお勧めします。

## <a name="vm-options"></a>VM のオプション

次の表では、Azure Data Explorer クラスター VM の技術的仕様を示します。

|**Name**| **カテゴリ** | **SSD サイズ** | **コア** | **RAM** | **Premium Storage ディスク (1 TB)**| **クラスターあたりの最小インスタンス数** | **クラスターあたりの最大インスタンス数**
|---|---|---|---|---|---|---|---
|D11_v2| コンピューティング最適化 | 75 GB    | 2 | 14 GB | 0 | 1 | 8 (1 である Dev/Test SKU を除く)
|D12_v2| コンピューティング最適化 | 150 GB   | 4 | 28 GB | 0 | 2 | 16
|D13_v2| コンピューティング最適化 | 307 GB   | 8 | 56 GB | 0 | 2 | 1,000
|D14_v2| コンピューティング最適化 | 614 GB   | 16| 112 GB | 0 | 2 | 1,000
|DS13_v2 + 1TB PS| ストレージ最適化 | 1 TB (テラバイト) | 8 | 56 GB | 1 | 2 | 1,000
|DS13_v2 + 2TB PS| ストレージ最適化 | 2 TB | 8 | 56 GB | 2 | 2 | 1,000
|DS14_v2 + 3TB PS| ストレージ最適化 | 3 TB | 16 | 112 GB | 2 | 2 | 1,000
|DS14_v2 + 4TB PS| ストレージ最適化 | 4 TB | 16 | 112 GB | 4 | 2 | 1,000
|L4s_v1| ストレージ最適化 | 650 GB | 4 | 32 GB | 0 | 2 | 16
|L8s_v1| ストレージ最適化 | 1.3 TB | 8 | 64 GB | 0 | 2 | 1,000
|L16s_1| ストレージ最適化 | 2.6 TB | 16| 128 GB | 0 | 2 | 1,000

* Azure Data Explorer [ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet) を使用するリージョンごとの更新された VM SKU の一覧を参照してください。 
* [さまざまなコンピューティング SKU](/azure/virtual-machines/windows/sizes-compute) の詳細を確認してください。 

## <a name="next-steps"></a>次の手順

* エンジン クラスターは、異なるニーズに応じて VM SKU を変更することで、いつでも[スケールアップまたはスケールダウン](manage-cluster-vertical-scaling.md)できます。 

* エンジン クラスターのサイズは、変化する需要に応じて容量を変更するため、[スケールインおよびスケールアウト](manage-cluster-horizontal-scaling.md)できます。

