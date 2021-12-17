---
title: QnA Maker マネージドからカスタム質問と回答への名前の変更
titleSuffix: Azure Cognitive Services
description: この記事には、QnA Maker の機能の変更に関するニュースが含まれています。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/11/2021
ms.custom: references_regions
ms.openlocfilehash: 6e8f9a26836be14d4952ba309933511635ff673a
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111982648"
---
# <a name="qna-maker-managed-is-now-renamed-to-custom-question-answering"></a>QnA Maker マネージドからカスタム質問と回答への名前の変更

[QnA Maker マネージド (プレビュー)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-qna-maker-managed-now-in-public-preview/ba-p/1845575) は、無料のパブリック プレビュー オファリングとして 2020 年 11 月に開始されました。 ディープ ラーニング ランカーを使用した関連性の強化、正確な回答、エンドツーエンドのリージョン サポートなど、いくつかの新機能が導入されました。 

Cognitive Services からの言語オファリングを統合する取り組みの一環として、QnA Maker マネージドは Text Analytics 内の機能になり、カスタム質問と回答に名前が変更されました。  

## <a name="creating-a-new-custom-question-answering-service"></a>新しいカスタム質問と回答サービスの作成

質問と回答およびエンティティ認識や感情分析などのその他の機能を使用するには、[Text Analytics リソースを作成します](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)。  

新しい Text Analytics リソースを作成するときに、含める機能を選択できます。 **カスタム質問と回答 (プレビュー)** を選択して、リソースの作成を続けます。  

> [!div class="mx-imgBorder"]
> [ ![カスタム質問と回答機能が選択されている Text Analytics リソース作成 UI メニューのスクリーンショット]( ./media/select-feature.png) ]( ./media/select-feature.png#lightbox)


QnA Maker 作成フローから QnA Maker マネージド リソースを作成することはできなくなりました。代わりに、Text Analytics サービスにリダイレクトされます。 QnA Maker の安定したリリースに対する変更はありません。 

> [!div class="mx-imgBorder"]
> [ ![リソース作成メニューのスクリーンショット]( ./media/create-resource.png) ]( ./media/create-resource.png#lightbox)

## <a name="details"></a>詳細

- 既存のすべての QnA Maker マネージド (プレビュー) リソースは、これまでと同様に引き続き機能します。 現時点では、これらのリソースに必要なアクションはありません。
- カスタム質問と回答 (プレビュー) の作成フローが主な変更です。 サービス、ポータル、エンドポイント、SDK などは、以前と同じままです。
- カスタム質問と回答 (プレビュー) は、引き続き無料のパブリック プレビューとして提供されます。 この機能は Text Analytics Standard リソースの一部としてのみ使用できます。 Text Analytics リソースの価格レベルを free に変更しないでください。
- カスタム質問と回答 (プレビュー) は、次のリージョンで使用できます。
    - 米国中南部
    - 北ヨーロッパ
    - オーストラリア東部

## <a name="next-steps"></a>次の手順

* [QnA Maker クライアント ライブラリの概要](./quickstarts/quickstart-sdk.md)
* [QnA Maker ポータルの概要](./quickstarts/create-publish-knowledge-base.md)

