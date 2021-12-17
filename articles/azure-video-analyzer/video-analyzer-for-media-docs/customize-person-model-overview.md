---
title: Azure Video Analyzer for Media (旧Video Indexer) で人物モデルをカスタマイズする - Azure
description: この記事では、Azure Video Analyzer for Media (旧 Video Indexer) における人物モデルについて、およびそのカスタマイズ方法について概要を示します。
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 1d76c1124be542a22707b358ce1d050dfac380f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614866"
---
# <a name="customize-a-person-model-in-video-analyzer-for-media"></a>Video Analyzer for Media で人物モデルをカスタマイズする

Azure Video Analyzer for Media (旧称 Video Indexer) では、ビデオに映っている有名人を認識できます。 著名人認識機能では、IMDB、Wikipedia、LinkedIn のトップ インフルエンサなどの一般的に要求されているデータ ソースに基づく約 100 万人の顔を処理できます。 Video Analyzer for Media によって認識されない顔も検出されますが、名前が付かないままになります。 お客様は、カスタム人物モデルを構築し、Video Analyzer for Media で既定では認識されない顔が認識されるようにすることができます。 お客様は、個人の名前と個人の顔の画像ファイルをペアにすることで、これらの人物モデルを構築できます。  

ご利用のアカウントがさまざまなユースケースに対応している場合は、アカウントごとに複数の人物モデルを作成できるベネフィットがあります。 たとえば、アカウントのコンテンツが異なるチャネルに並べ替えられるものの場合は、チャネルごとに異なる人物モデルを作成できます。 

> [!NOTE]
> 各人物モデルでは最大 100万人の人物がサポートされ、各アカウントは 50 人物モデルに制限されます。 

モデルが作成されたら、ビデオのアップロード/インデックスの作成または再作成の際に、特定の人物モデルのモデル ID を指定して使用できます。 ビデオの新しい顔のトレーニングを行うと、そのビデオが関連付けられた特定のカスタム モデルが更新されます。 

複数の人物モデルのサポートが必要でない場合は、アップロード/インデックスの作成または再作成の際にビデオに人物モデル ID を割り当てないでください。 この場合、Video Analyzer for Media ではアカウントの既定の人物モデルが使われます。 

Video Analyzer for Media の Web サイトでは、ビデオで検出された顔を編集し、自分のアカウントの複数のカスタム人物モデルを管理できます。[Web サイトを使用した人物モデルのカスタマイズ](customize-person-model-with-website.md)に関するトピックの説明をご覧ください。  [API を使用した人物モデルのカスタマイズ](customize-person-model-with-api.md)に関する記事の説明に従って、API を使用することもできます。
