---
title: 需要の変化に対応するために Azure データ エクスプローラー クラスターをスケーリングする
description: この記事では、需要の変化に基づいて、Azure データ エクスプローラー クラスターをスケールアウトおよびスケールインする手順について説明します。
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 213a49d87d5e9f801bb17604a322c231a4e3dee2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735315"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>需要の変化に対応するようにクラスターのスケールアップを管理する

クラスターのサイズを適切に設定することは、Azure データ エクスプローラーのパフォーマンスにとって重要なことです。 しかし、クラスターに対する需要を 100% の精度で予測することはできません。 静的クラスターのサイズが使用率の低下や超過の原因となる可能性があり、どちらも理想的な状態ではありません。 変化する需要に合わせて容量と CPU を追加および削除して、クラスターを*スケーリング*することをお勧めします。 この記事では、クラスターのスケールアップを管理する方法を示します。

ご利用のクラスターに移動して、**[設定]** で **[スケールアップ]** を選択します。

使用可能な SKU の一覧が表示されます。 有効なカードの一覧から選択することができます。 たとえば、次の図で、D14_vs から選択できる SKU は 1 つだけです。

![スケールアップ](media/manage-cluster-scale-up/scale-up.png)

D13_v2 は、クラスターの現在の SKU のため、無効になっています。 L8 と L16 は、クラスターがあるリージョンでは使用できないため、無効になっています。

SKU を変更するには、使用する SKU をクリックして、**[選択]** ボタンをクリックします。

[!NOTE] スケールアップ プロセスには数分かかる可能性があり、その間クラスターは停止されます。 スケールダウンは、クラスターのパフォーマンスに害を与える可能性があるので注意してください。

クラスターのスケーリングに関する問題が発生したときにサポートが必要な場合は、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) でサポート要求を開いてください。