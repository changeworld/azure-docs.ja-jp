---
title: Video Indexer を使用してビデオ内の正確な場面を検索する
titleSuffix: Azure Media Services
description: Video Indexer を使用してビデオ内の正確な場面を検索する方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 1c3326456fefbbef9228cf26b5821f306cd21601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127903"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>Video Indexer を使用してビデオ内の正確な場面を検索する

このトピックでは、ビデオ内の正確な場面を検索することができる検索オプションを示します。

1. [Video Indexer](https://www.videoindexer.ai/) の Web サイトに移動してサインインします。
2. アカウントのすべてのビデオから検索します。

    次の例では、セキュリティについて説明した、Satya が出演しているビデオをすべて検索します。

    ![Video Indexer でビデオを検索する](./media/video-indexer-search/video-indexer-search01.png)

3. ビデオの要約された分析情報を検索します。

    その後、ビデオの **[再生]** を選択して、ビデオ内を検索することができます。 次に、 **[検索]** タブを選択して、ビデオ内の正確な場面を検索できます。

    次の例では、選択したビデオ内の「セキュア」を検索します。

    ![Video Indexer を使用してビデオ内を検索する](./media/video-indexer-search/video-indexer-search02.png)

    結果の 1 つを選択すると、プレーヤーに、ビデオ内のその正確な場面が表示されます。 アプリ内で、プレーヤー/分析情報の表示と同期を実現できます。 詳細については、[アプリに Video Indexer ウィジェットを埋め込む](video-indexer-embed-widgets.md)ことに関するページを参照してください。

4. ビデオの詳細な内訳を検索します。

    見つけたビデオに基づいて独自のクリップを作成する場合は、 **[編集]** ボタンを選択します。 このページには、ビデオと一緒に、フィルターとしての分析情報が表示されます。 詳細については、「[Video Indexer の分析情報を表示および編集する](video-indexer-view-edit.md)」を参照してください。

    ビデオ内で正確な場面を検索し、関心のある台詞のみを表示できます。 横の分析情報を使用して、表示するパーツをフィルターします。 完了したら、クリップをプレビューし、 **[発行]** を選択してギャラリーに表示される新しいクリップを作成できます。

    次の例では、「mixed reality」テキストを検索します。 また、下の画面に示すように追加のフィルターも適用しました。

    ![Video Indexer で正確な場面を検索する](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>次のステップ

作業したいビデオ内の正確な場面を見つけたら、ビデオの処理を続行できます。 詳細については、次のトピックを参照してください。

- [ビデオの詳細な分析情報を使用する](use-editor-create-project.md)
- [Video Indexer REST API を使用してコンテンツを処理する](video-indexer-use-apis.md)
- [アプリケーションにビジュアル ウィジェットを埋め込む](video-indexer-embed-widgets.md)

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md)
