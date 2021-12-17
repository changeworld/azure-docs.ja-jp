---
title: Azure Video Analyzer での AI コンポジション
description: この記事では、Azure Video Analyzer による 3 種類の AI コンポジションのサポートの概要について説明します。 このトピックでは、AI コンポジションの種類ごとにシナリオの説明も提供します。
ms.topic: conceptual
ms.date: 11/04/2021
ms.openlocfilehash: 4195fd0dbcd44f0eeb273384e6d6cfad352aedb5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847111"
---
# <a name="ai-composition"></a>AI コンポジション

この記事では、Azure Video Analyzer による 3 種類の AI コンポジションのサポートの概要について説明します。 

* [逐次](#sequential-ai-composition)
* [Parallel](#parallel-ai-composition)
* [結合済み](#combined-ai-composition)

## <a name="sequential-ai-composition"></a>順次 AI コンポジション

AI ノードを順番に構成できます。 これにより、アップストリーム ノードによって生成された推論をダウンストリーム ノードで拡張できます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/sequential.svg" alt-text="順次 AI コンポジション":::
 
### <a name="key-aspects"></a>重要な点

* パイプライン拡張機能はメディア パススルー ノードとして機能し、外部 AI サーバーが異なるレート、形式、解像度でフレームを受信するように構成できます。 さらに、外部 AI サーバーがすべてのフレームまたは既に推論が含まれるフレームのみを受信できるように、構成を指定できます。
* フレームがさまざまな拡張ノードを通過する過程で推論が追加され、このようなノードの数に制限はありません。
* 継続的なビデオ記録やイベント ベースのビデオ記録などの他のシナリオを、順次 AI コンポジションと組み合わせることができます。

    
## <a name="parallel-ai-composition"></a>並列 AI コンポジション

AI ノードは、順次ではなく並列に構成することもできます。 これにより、取り込まれたビデオ ストリームに対して独立した推論を実行でき、エッジでの取り込み帯域幅を節約できます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/parallel.svg" alt-text="並列 AI コンポジション":::
 
### <a name="key-aspects"></a>重要な点

* ビデオを任意の数の並列ブランチに分割でき、このような分割は、次のノードの後の任意の時点で実行できます。
    
    * RTSP ソース
    * Motion Detector
    * パイプライン拡張

## <a name="combined-ai-composition"></a>結合 AI コンポジション

順次と並列の両方のコンポジション構造を結合して、複雑な構成可能 AI パイプラインを開発できます。 これは、AVA パイプラインを使用すると、拡張ノードを順番に結合したり、サポートされている他のノードと無制限に並列に結合したできるため、可能になります。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/complex.svg" alt-text="結合 AI コンポジション":::
 


## <a name="next-steps"></a>次のステップ

[AI コンポジションを使用した複数の AI モデルによるライブ ビデオ ストリームの分析](analyze-ai-composition.md)
