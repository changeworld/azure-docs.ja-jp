---
title: Video Indexer でのブランド モデルのカスタマイズ - Azure
titlesuffix: Azure Media Services
description: この記事では、Video Indexer におけるブランド モデルについて説明し、そのカスタマイズ方法の概要を示します。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 7df709adbd8e45712c112b52fc76920f8b67fe91
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53282958"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Video Indexer でのブランド モデルのカスタマイズ

Video Indexer では、動画と音声コンテンツのインデックス作成および再作成中に、音声と視覚的テキストからのブランドの検出をサポートします。 ブランド検出機能を使用すると、Bing のブランドのデータベースによって示唆される製品、サービス、および企業が識別されます。 たとえば、動画または音声コンテンツで Microsoft に言及されている場合や、動画内の視覚テキストに表示される場合、Video Indexer は、そのコンテンツ内でブランドとして Microsoft を検出します。 ブランドは、コンテキストを使用して、他の用語と区別されます。

ブランドの検出は、コンテンツのアーカイブと検出、コンテキストに応じた広告、ソーシャル メディア分析、小売りの競合分析などの多くのさまざまなビジネス シナリオで役立ちます。 Video Indexer のブランド検出機能を使用すると、Bing のブランド データベースを使って音声や視覚テキストでのブランドの言及にインデックスを付けたり、Video Indexer のアカウントごとにカスタム ブランド モデルを作成してカスタマイズしたりできます。 カスタム ブランド モデル機能を使用して、Video Indexer で Bing ブランド データベースのブランドを検出するかどうかの選択、検出される特定のブランドの除外 (実質的にブランドのブラック リストの作成)、Bing のブランド データベースにない可能性があるがモデルの一部にする必要のあるブランドの包含 (実質的にブランドのホワイト リストの作成) を行うことができます。

## <a name="out-of-the-box-detection-example"></a>そのまま使用できる検出の例

[Microsoft Build 2017 Day 2](https://www.videoindexer.ai/media/ed6ede78ad/) のプレゼンテーションでは、ブランド "Microsoft Windows" が複数回出現します。 トランスクリプト内や視覚テキストでときどき使用されていますが、文字通りに使用されているわけではありません。 Video Indexer では、コンテキストに基づいて、用語が本当にブランドであることを高い精度で検出します。すぐに使える 90,000 件のブランドが網羅されていて、絶えず更新されています。 Video Indexer は、02:25 に音声からブランドを検出し、02:40 に Windows ロゴの一部である視覚テキストから再度検出します。

![ブランドの概要](./media/content-model-customization/brands-overview.png)

コンテキストからあいまいさを解消する方法を熟知している高度な機械学習アルゴリズムに基づくため、建築のコンテキストで "windows" について話しても、"Windows" はブランドとして検出されません。Box、Apple、Fox などでも同様です。 ブランドの検出は、サポートされているすべての言語で機能します。 Microsoft Build 2017 Day 2 の基調講演の動画とインデックスの完全版については、[ここ](http://www.videoindexer.ai/media/ed6ede78ad/)をクリックしてください。

独自のブランドを使用するには、「[次の手順](#next-stpes)」を確認してください。

## <a name="next-steps"></a>次の手順

[API を使用してブランド モデルをカスタマイズする](customize-brands-model-with-api.md)

[Web サイトを使用してブランド モデルをカスタマイズする](customize-brands-model-with-website.md)
