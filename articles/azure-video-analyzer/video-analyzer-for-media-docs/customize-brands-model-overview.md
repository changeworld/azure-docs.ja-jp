---
title: Azure Video Analyzer for Media (旧 Video Indexer) でブランド モデルをカスタマイズする - Azure
description: この記事では、Azure Video Analyzer for Media (旧 Video Indexer) におけるブランド モデルについて、およびそのカスタマイズ方法について概要を示します。
ms.topic: conceptual
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: b698bce219468ee44a78aea7b8e449b603ecd975
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665665"
---
# <a name="customize-a-brands-model-in-video-analyzer-for-media"></a>Video Analyzer for Media でブランド モデルをカスタマイズする

Azure Video Analyzer for Media (旧 Video Indexer) では、ビデオとオーディオ コンテンツのインデックスの作成と再作成中に、音声と視覚テキストからのブランドの検出がサポートされます。 ブランド検出機能では、Bing のブランド データベースで提案される製品、サービス、および企業の説明が識別されます。 たとえば、Microsoft がビデオまたはオーディオ コンテンツで説明されている場合や、ビデオ内の視覚テキストに表示される場合、Video Analyzer for Media ではそのコンテンツ内でブランドとして Microsoft が検出されます。 ブランドは、コンテキストを使用して、他の用語と区別されます。

ブランドの検出は、コンテンツのアーカイブと検出、コンテキストに応じた広告、ソーシャル メディア分析、小売りの競合分析などの多くのさまざまなビジネス シナリオで役立ちます。 Video Analyzer for Media のブランド検出機能を使用すると、Bing のブランド データベースを使って音声や視覚テキストでのブランドの言及にインデックスを付けたり、Video Analyzer for Media のアカウントごとにカスタム ブランド モデルを作成してカスタマイズしたりできます。 カスタム ブランド モデル機能を使用して、Video Analyzer for Media で Bing ブランド データベースのブランドを検出するかどうかの選択、検出される特定のブランドの除外 (実質的にブランドのブラック リストの作成)、Bing のブランド データベースにない可能性があるがモデルの一部にする必要のあるブランドの包含 (実質的に承認済みブランドのリストの作成) を行うことができます。 作成したカスタム ブランド モデルは、モデルを作成したアカウントでのみ使用できます。

## <a name="out-of-the-box-detection-example"></a>そのまま使用できる検出の例

"Microsoft Build 2017 Day 2" のプレゼンテーションでは、ブランド "Microsoft Windows" が複数回出現します。 トランスクリプト内や視覚テキストでときどき使用されていますが、文字通りに使用されているわけではありません。 Video Analyzer for Media では、コンテキストに基づいて、用語が本当にブランドであることを高い精度で検出します。すぐに使える 90,000 件のブランドが網羅されていて、絶えず更新されています。 Video Analyzer for Media は、02:25 に音声からブランドを検出し、02:40 に Windows ロゴの一部である視覚テキストから再度検出します。

![ブランドの概要](./media/content-model-customization/brands-overview.png)

コンテキストからあいまいさを解消する方法を熟知している高度な機械学習アルゴリズムに基づくため、建築のコンテキストで "windows" について話しても、"Windows" はブランドとして検出されません。Box、Apple、Fox などでも同様です。 ブランドの検出は、サポートされているすべての言語で機能します。  

## <a name="next-steps"></a>次のステップ

独自のブランドを使用するには、次のトピックを参照してください。

[API を使用してブランド モデルをカスタマイズする](customize-brands-model-with-api.md)

[Web サイトを使用してブランド モデルをカスタマイズする](customize-brands-model-with-website.md)
