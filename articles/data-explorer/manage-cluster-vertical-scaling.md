---
title: 需要の変化に対応するために Azure Data Explorer のクラスターの垂直スケーリング (スケールアップ) を管理する
description: この記事では、需要の変化に基づいて、Azure Data Explorer クラスターをスケールアップおよびスケールダウンする手順について説明します。
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 80bbdf3a5d936719b06782cd78d56088b36cb21d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985476"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>需要の変化に対応するために Azure Data Explorer のクラスターの垂直スケーリング (スケールアップ) を管理する

クラスターのサイズを適切に設定することは、Azure データ エクスプローラーのパフォーマンスにとって重要なことです。 静的クラスターのサイズが使用率の低下や超過の原因となる可能性があり、どちらも理想的な状態ではありません。

クラスターに対する需要を高い精度で予測することはできないため、変化する需要に合わせて容量と CPU リソースを追加および削除して、クラスターを*スケーリング*することをお勧めします。 

Azure Data Explorer クラスターのスケーリングには、2 つのワークフローがあります。

* [水平スケーリング](manage-cluster-horizontal-scaling.md) (スケールアウトとスケールインともいう)。
* 垂直スケーリング (スケールアップとスケールダウンともいう)。

この記事では、垂直スケーリングのワークフローについて説明します。

## <a name="configure-vertical-scaling"></a>垂直スケーリングを構成する

1. Azure portal で、Azure Data Explorer クラスター リソースに移動します。 **[設定]** で、 **[スケール アップ]** を選択します。

1. **[スケールアップ]** ウィンドウに、クラスターで使用可能な SKU の一覧が表示されます。 たとえば、次の図では、利用可能な SKU は 4 つのみです。

    ![スケールアップ](media/manage-cluster-vertical-scaling/scale-up.png)

    SKU が無効になっているのは、それが現在の SKU であるか、そのクラスターが配置されているリージョンで利用できないためです。

1. SKU を変更するには、新しい SKU を選択して、 **[選択]** をクリックします。

> [!NOTE]
> * 垂直スケーリング プロセスには数分かかる可能性があり、その間、クラスターは停止されます。 
> * スケールダウンするとクラスターのパフォーマンスが低下することがあります。
> * 料金は、クラスターの仮想マシンと Azure Data Explorer のサービス コストの見積もりです。 その他のコストは含まれません。 見積もりについては、Azure Data Explorer の[コスト見積もりツール](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)のページを参照してください。価格に関する情報は、Azure Data Explorer の[価格に関するページ](https://azure.microsoft.com/pricing/details/data-explorer/)を参照してください。

これで、Azure Data Explorer クラスターの垂直スケーリングが構成されました。 水平スケーリング用に別のルールを追加します。 クラスターのスケーリングに関する問題が発生したときにサポートが必要な場合は、Azure portal で[サポート要求を開いてください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

## <a name="next-steps"></a>次の手順

* [クラスターの水平スケーリングを管理](manage-cluster-horizontal-scaling.md)して、指定したメトリックに基づいてインスタンス数を動的にスケールアウトします。

* この記事に従って、リソース使用量を監視します。[メトリックを使用した Azure Data Explorer のパフォーマンス、正常性、および使用状況の監視](using-metrics.md)。

