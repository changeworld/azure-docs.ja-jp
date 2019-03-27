---
title: 需要の変化に対応するために Azure データ エクスプローラー クラスターをスケーリングする
description: この記事では、需要の変化に基づいて、Azure Data Explorer クラスターをスケールアップおよびスケールダウンする手順について説明します。
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: bc3f97c798f5e040908e8103c00d3f015f8c824d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415336"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>需要の変化に対応するようにクラスターのスケールアップを管理する

クラスターのサイズを適切に設定することは、Azure データ エクスプローラーのパフォーマンスにとって重要なことです。 しかし、クラスターに対する需要を 100% の精度で予測することはできません。 静的クラスターのサイズが使用率の低下や超過の原因となる可能性があり、どちらも理想的な状態ではありません。 変化する需要に合わせて容量と CPU を追加および削除して、クラスターを*スケーリング*することをお勧めします。 スケーリングには、スケールアップとスケールアウトの 2 つのワークフローがあります。この記事では、クラスターのスケールアップを管理する方法を示します。

1. ご利用のクラスターに移動して、**[設定]** で **[スケールアップ]** を選択します。

    利用可能な SKU の一覧が表示されます。 たとえば、以下の図では、利用可能な SKU は次の 1 つだけです: D14_V2。

    ![スケールアップ](media/manage-cluster-scale-up/scale-up.png)

    D13_V2 は、クラスターの現在の SKU のため、無効になっています。 L8 と L16 は、クラスターが配置されているリージョンでは利用できないため、無効になっています。

1. SKU を変更するには、目的の SKU を選択して、**[選択]** ボタンをクリックします。

> [!NOTE]
> スケールアップ プロセスには数分かかる可能性があり、その間クラスターは停止されます。 スケールダウンは、クラスターのパフォーマンスに害を与える可能性があるので注意してください。

これで、Azure Data Explorer クラスターのスケールアップまたはスケールダウン操作を実行しました。 [クラスターのスケールアウト](manage-cluster-scale-out.md) (自動スケーリングとも呼ばれます) を行い、指定したメトリックに基づいて動的にスケーリングすることもできます。

クラスターのスケーリングに関する問題が発生したときにサポートが必要な場合は、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) でサポート要求を開いてください。
