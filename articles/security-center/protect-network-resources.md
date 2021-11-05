---
title: Microsoft Defender for Cloud でのネットワーク リソースの保護
description: このドキュメントでは、Microsoft Defender for Cloud での推奨事項に従って Azure ネットワーク リソースを保護し、セキュリティ ポリシーでコンプライアンスを順守する方法について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8830ff93a8f75109d9396bb3155c5edcb8343718
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091615"
---
# <a name="protect-your-network-resources"></a>ネットワーク リソースの保護

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud により、ネットワーク セキュリティのベスト プラクティスに対して Azure リソースのセキュリティ状態が継続的に分析されます。 Defender for Cloud によって潜在的なセキュリティの脆弱性が識別されると、リソースを堅牢化および保護するために必要な管理を構成するプロセスを説明する推奨事項が作成されます。

ネットワークに関するすべての推奨事項の覧については、[ネットワークに関する推奨事項](recommendations-reference.md#recs-networking)のページを参照してください。

この記事では、ネットワーク セキュリティの観点から Azure リソースに適用される推奨事項について説明します。 ネットワークに関する推奨事項は、次世代ファイアウォール、ネットワーク セキュリティ グループ、JIT VM アクセス、過度に許容されるインバウンド トラフィック規則などが中心です。 ネットワークの推奨事項と修復アクションの一覧については、[Microsoft Defender for Cloud でのセキュリティに関する推奨事項の管理](review-security-recommendations.md)に関するページをご覧ください。

Defender for Cloud の **ネットワーク** 機能は次のとおりです。 

- ネットワーク マップ (Microsoft Defender for servers が必要)
- [アダプティブ ネットワークのセキュリティ強化](adaptive-network-hardening.md) (Microsoft Defender for servers が必要)
- ネットワーク セキュリティに関する推奨事項
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>ネットワーク リソースとその推奨事項の表示

[資産インベントリ ページ](asset-inventory.md)で、リソースの種類のフィルターを使用して、調査するネットワーク リソースを選択します。

:::image type="content" source="./media/protect-network-resources/network-filters-inventory.png" alt-text="資産インベントリのネットワーク リソースの種類。" lightbox="./media/protect-network-resources/network-filters-inventory.png":::


## <a name="network-map"></a>ネットワーク マップ

対話型のネットワーク マップには、ネットワーク リソースを堅牢化するための推奨事項と分析情報を提供するセキュリティ オーバーレイを含むグラフィカル ビューが表示されます。 マップを使用して、Azure ワークロードのネットワーク トポロジ、仮想マシンとサブネット間の接続、およびマップから特定のリソースとそれらのリソースの推奨事項にドリル ダウンする機能を表示できます。

ネットワーク マップを開くには:

1. Defender for Cloud のメニューで、 **[ワークロード保護]** ダッシュボードを開きます。

1. **[ネットワーク マップ]** を選びます。

    :::image type="content" source="./media/protect-network-resources/opening-network-map.png" alt-text="ワークロード保護からネットワーク マップを開く。" lightbox="./media/protect-network-resources/opening-network-map.png":::

1. **[レイヤー]** メニューを選択し、 **[トポロジ]** を選択します。
 
トポロジ マップの既定のビューが表示されます。

- 現在選択されているサブスクリプション - マップは、ポータルで選択したサブスクリプションに対して最適化されます。 選択を変更すると、新しい選択内容でマップが再生成されます。
- Resource Manager リソースの種類の VM、サブネット、VNet ("クラシック" Azure リソースはサポートされていません)
- ピアリング VNet
- 重大度が高または中の[ネットワークに関する推奨事項](review-security-recommendations.md)を持つリソースのみ
- インターネットに接続するリソース

:::image type="content" source="./media/protect-network-resources/network-map-info.png" alt-text="Defender for Cloud のネットワーク トポロジ マップのスクリーンショット。" lightbox="./media/protect-network-resources/network-map-info.png":::

## <a name="understanding-the-network-map"></a>ネットワーク マップについて

ネットワーク マップでは、 **[トポロジ]** ビューと **[トラフィック]** ビューに Azure リソースを表示できます。 

### <a name="the-topology-view"></a>[トポロジ] ビュー

ネットワーク マップの **[トポロジ]** ビューでは、ネットワーク リソースに関する次の分析情報を参照できます。

- 内側の円では、選択したサブスクリプション内のすべての Vnet を確認できます、次の円はすべてのサブネットで、外側の円はすべての仮想マシンです。
- マップ内のリソースを結ぶ線を見ると、互いに関連付けられているリソースと、Azure ネットワークの構成方法を把握できます。 
- 重大度インジケーターを使用して、Defender for Cloud からの未処理の推奨事項があるリソースの概要をすばやく取得できます。
- いずれかのリソースをクリックしてドリル ダウンし、そのリソースの詳細と推奨事項を直接表示できます。ネットワーク マップのコンテキストで表示することもできます。  
- マップに表示されているリソースが多すぎる場合、Microsoft Defender for Cloud により独自のアルゴリズムを使用してリソースがスマートにクラスター化され、最もクリティカルな状態のリソースと、最も重大度の高い推奨事項があるリソースが強調表示されます。

マップは対話型かつ動的なので、すべてのノードがクリック可能で、ビューはフィルターに基づいて変化することがあります。

1. 上部にあるフィルターを使用して、ネットワーク マップに表示される内容を変更できます。 次の項目に基づいてマップのフォーカスを設定できます。

   -  **セキュリティ正常性**: Azure リソースの重大度 (高、中、低) に基づいてマップをフィルター処理できます。
   - **推奨事項**: リソースに対してアクティブになっている推奨事項に基づいて、表示されるリソースを選択できます。 たとえば、Defender for Cloud によってネットワーク セキュリティ グループの有効化が推奨されているリソースのみを表示できます。
   - **ネットワーク ゾーン**: 既定では、マップにはインターネットに接続しているリソースのみが表示され、内部 VM も選択できます。
 
2. いつでも左上隅の **[リセット]** をクリックして、マップを既定の状態に戻すことができます。

リソースにドリル ダウンするには:

1. マップ上の特定のリソースを選択すると、右側のウィンドウが開き、リソースに関する全般情報、接続されたセキュリティ ソリューション (ある場合)、およびリソースに関する推奨事項が表示されます。 この動作の種類は選択したリソースの種類ごとに同じです。 
2. マップ内のノードをポイントすると、サブスクリプション、リソースの種類、リソース グループなど、リソースに関する全般情報を表示できます。
3. リンクを使用してツール ヒントにズームインし、その特定のノードにマップのフォーカスを再設定します。 
4. マップのフォーカスを特定のノードから移動するには、ズームアウトします。

### <a name="the-traffic-view"></a>[トラフィック] ビュー

**[トラフィック]** ビューには、リソース間で可能なすべてのトラフィックのマップが表示されます。 これにより、どのリソースがどのユーザーと通信できるかを定義する、構成したすべてのルールのビジュアル マップが提供されます。 このため、ネットワーク セキュリティ グループの既存の構成に加えて、ワークロード内で潜在リスクのある構成をすばやく識別できます。

### <a name="uncover-unwanted-connections"></a>望ましくない接続の検出

このビューの強みは、これらの許可された接続を、存在する脆弱性と共に示せることです。このさまざまなデータを使用して、リソースに対して必要な堅牢化を実行できます。 

たとえば、認識していなかった 2 台のマシンが通信できることを検出し、ワークロードとサブネットをより適切に分離できる可能性があります。

### <a name="investigate-resources"></a>リソースの調査

リソースにドリル ダウンするには:

1. マップ上の特定のリソースを選択すると、右側のウィンドウが開き、リソースに関する全般情報、接続されたセキュリティ ソリューション (ある場合)、およびリソースに関する推奨事項が表示されます。 この動作の種類は選択したリソースの種類ごとに同じです。 
2. **[トラフィック]** をクリックして、リソースに対して可能な送信および受信トラフィックの一覧を表示します。これは、リソースと通信できるユーザーやリソースが通信できるユーザー、経由するプロトコルとポートの包括的な一覧です。 たとえば、VM を選択すると、その VM と通信できるすべての VM が表示され、サブネットを選択すると、そのサブネットと通信できるすべてのサブネットが表示されます。

**このデータは、ネットワーク セキュリティ グループの分析と、交差と相互作用を把握するために複数のルールを分析する高度な機械学習アルゴリズムに基づいています。** 

[![ネットワーク トラフィック マップ。](./media/protect-network-resources/network-map-traffic.png)](./media/protect-network-resources/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>次のステップ

その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

- [Microsoft Defender for Cloud でのマシンとアプリケーションの保護](./asset-inventory.md)
