---
title: "Azure API Management インスタンスのアップグレードとスケーリングを行う | Microsoft Docs"
description: "このトピックでは、Azure API Management インスタンスのアップグレードとスケーリングを行う方法について説明します。"
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 22cc917eb6f296724bf535e48b0dd6ba8927e5d3
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2017
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>API Management インスタンスのアップグレードとスケーリングを行う 

API Management (APIM) インスタンスは、ユニットを追加するか削除することでスケーリングできます。 **ユニット**は専用の Azure リソースで構成され、1 か月あたりの API 呼び出しの数として表される特定の耐荷容量があります。 実際のスループットと待機時間は、同時接続の数とレート、構成されたポリシーの種類と数、要求のサイズと応答のサイズ、バックエンドの待機時間などの多くの要因によって、大幅に異なります。 

各ユニットの容量と価格は、ユニットが存在する**レベル**によって決まります。 3 つのレベル (**Developer**、**Standard**、**Premium**) から選択できます。 レベル内でサービスの容量を増やす必要がある場合は、ユニットを追加する必要があります。 現在選択されている APIM インスタンスのレベルではそれ以上ユニットを追加できない場合は、上位のレベルにアップグレードする必要があります。 

各ユニットの価格、ユニットを追加/削除する能力、特定の機能 (複数リージョンへのデプロイなど) があるかどうかは、APIM インスタンス用に選択したレベルによって異なります。 [価格の詳細](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)に関する記事で、ユニットあたりの価格と各レベルで利用できる機能が説明されています。 

>[!NOTE]
>[価格の詳細](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)に関する記事では、各レベルにおけるユニットの容量の概算の数値が示されています。 正確な数値を取得するには、API の現実的なシナリオを検討する必要があります。 この後の「容量を計画する方法」セクションを参照してください。

## <a name="prerequisites"></a>前提条件

この記事で説明されている手順を実行するには、以下が必要です。

+ 有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM インスタンス。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。

## <a name="how-to-plan-for-capacity"></a>容量を計画する方法

トラフィックを処理するための十分なユニットがあるかどうかを確認するには、予想されるワークロードでテストを実行します。 

前述のように、APIM が秒あたりで処理できる要求の数は、多数の変数によって異なります。 変数には、接続パターン、要求と応答のサイズ、各 API で構成されているポリシー、要求を送信するクライアントの数などがあります。

特定の時点で使用されている容量を理解するには、**メトリック** を使用します (Azure Monitor の機能を使用します)。

### <a name="use-the-azure-portal-to-examine-metrics"></a>Azure ポータルを使用してメトリックを調べる 

1. [Azure ポータル](https://portal.azure.com/)で、APIM インスタンスに移動します。
2. **[メトリック]** を選びます。
3. **[使用可能なメトリック]** から **[容量]** メトリックを選択します。 

    容量メトリックは、テナントでどの程度の容量が使用されているのかを示します。 負荷をさらに増やしてテストすることで、ピーク時の負荷を確認できます。 メトリック アラートを設定して、予想外の出来事が発生したときに、それを知ることができます  (たとえば、APIM インスタンスが 5 分以上容量を超過していること)。 

    >[!TIP]
    > アラートを構成して、サービスの容量不足や、ユニットの追加によって自動的にスケーリングを行うロジック アプリの呼び出しが発生していることを知ることができます。

## <a name="upgrade-and-scale"></a>アップグレードとスケーリング 

前述のように、3 つのレベル (**Developer**、**Standard**、**Premium**) から選択できます。 **Developer** レベルは、サービスを評価するために使用する必要があります。運用環境では使用しないでください。 **Developer** レベルには SLA がなく、このレベルをスケーリング (ユニットの追加/削除) することはできません。 

**Standard** と **Premium** は運用レベルであり、SLA があり、スケーリングできます。 **Standard** レベルは、最大 4 つのユニットにスケーリングできます。 **Premium** レベルでは、任意の数のユニットを追加できます。 

**Premium** レベルでは、1 つの API management インスタンスを任意の数の Azure リージョンに配布できます。 API Management サービスを初めて作成すると、インスタンスにはユニットが 1 つだけ含まれ、そのインスタンスは 1 つの Azure リージョンに存在します。 最初のリージョンは、**プライマリ** リージョンとして指定されます。 別のリージョンを簡単に追加できます。 リージョンを追加するときに、割り当てるユニットの数を指定します。 たとえば、**プライマリ** リージョンに １ つのユニットを、別のリージョンに 5 つのユニットを割り当てることができます。 各リージョンで処理するトラフィックを調整できます。 詳細については、「[複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法](api-management-howto-deploy-multi-region.md)」を参照してください。

レベル間でアップグレードまたはダウングレードを実行できます。 ダウングレードすると、一部の機能が削除される可能性があることに注意してください。たとえば、Premium レベルから Standard レベルにダウングレードすると、VNET や複数リージョンへのデプロイは利用できなくなります。

>[!NOTE]
>アップグレードまたはスケーリング プロセスは、適用されるまで 15 ～ 30 分かかります。 終了すると、通知を受け取ります。

### <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Azure ポータルを使用してアップグレードとスケーリングを行う

1. [Azure ポータル](https://portal.azure.com/)で、APIM インスタンスに移動します。
2. **[スケールと料金]** を選択します。
3. 目的のレベルを選択します。
4. 追加する**ユニット**の数を指定します。 スライダーを使用するか、ユニットの数を入力できます。<br/>
    **Premium** レベルを選択した場合は、最初にリージョンを選択する必要があります。
5. **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ

[複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法](api-management-howto-deploy-multi-region.md)

