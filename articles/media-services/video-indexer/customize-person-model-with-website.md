---
title: Video Indexer Web サイトを使用して人物モデルをカスタマイズする - Azure
titlesuffix: Azure Media Services
description: この記事では、Video Indexer Web サイトを使用して人物モデルをカスタマイズする方法を示します。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 321db75b1a286e7de1e38ed3b382ee7dc9fe5902
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283748"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Video Indexer Web サイトを使用して人物モデルをカスタマイズする

Video Indexer では、ビデオ コンテンツの顔検出と著名人認識がサポートされています。 著名人認識機能では、IMDB、Wikipedia、LinkedIn のトップ インフルエンサなどの一般的に要求されているデータ ソースに基づく約 100 万人の顔を処理できます。 著名人認識機能によって認識されない顔も検出されますが、それらの顔では名前が未設定になります。 Video Indexer に動画をアップロードし、結果を取得した後、認識されなかった顔の名前を指定できます。 顔に名前のラベルを付けると、顔と名前がお使いのアカウントの人物モデルに追加されます。 その後、Video Indexer によって、その顔が今後の動画と過去の動画で認識されます。

Video Indexer Web サイトを使用すると、このトピックで説明されているように、動画内で検出された顔を編集できます。 「[Customize person model using APIs](customize-person-model-with-api.md)」(API を使用した人物モデルのカスタマイズ) の説明に従って、API を使用することもできます。

## <a name="edit-a-face"></a>顔を編集する

> [!NOTE]
> 名前は人物モデル内で一意であるため、2 つの異なる顔に同じ名前を指定した場合、Video Indexer ではこれらの顔は同じ人物であるとみなされ、動画のインデックスを再作成したときに 1 つに集約されます。 Video Indexer で同じ顔が異なる状況で複数回検出された場合は、同じ名前を指定し、動画のインデックスを再作成します。
> Video Indexer によって著名人として認識された顔を新しい名前に更新できます。 指定された新しい名前は、組み込みの著名人認識に優先します。

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。
2. 表示して編集する動画をお使いのアカウント内で検索します。
3. 動画内の顔を編集するには、**[Insights]** タブに移動し、ウィンドウの右上隅の鉛筆アイコンをクリックします。

    ![人物モデルをカスタマイズする](./media/customize-face-model/customize-face-model.png)

4. 検出された顔のいずれかをクリックし、名前を [不明 #X] (または前に顔に割り当てられていた名前) から別の名前に変更します。
5. 新しい名前を入力したら、新しい名前の横にあるチェックマーク アイコンをクリックします。 これにより、新しい名前が保存され、その時点で存在する他の動画または今後アップロードされる動画に出現したときにその顔が認識され、名前が付けられます。 その時点で存在する他の動画に含まれる顔の認識はバッチ プロセスであるため、有効になるまで少し時間がかかることがあります。 

    ![更新の保存](./media/customize-face-model/save-update.png)

## <a name="delete-a-face"></a>顔を削除する

動画内で検出された顔を削除するには、**[Insights]** ウィンドウに移動し、ウィンドウの右上隅にある鉛筆アイコンをクリックします。 顔の名前の下にある **[削除]** オプションをクリックします。 これにより、検出された顔が動画から削除されます。 この顔が出現する他の動画では、顔は引き続き検出されますが、動画のインデックスを作成した後、同じように削除できます。 顔を削除する前に名前を指定していた場合、その顔はお使いのアカウントの人物モデル内に引き続き存在します。

## <a name="next-steps"></a>次の手順

[API を使用して人物モデルをカスタマイズする](customize-person-model-with-api.md)
