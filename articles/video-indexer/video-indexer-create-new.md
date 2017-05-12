---
title: Use Azure Video Indexer to create video insights from existing videos | Microsoft Docs
description: This topic shows you how to create and publish video insights based on some other video.
services: cognitive services
documentationcenter: 
author: juliako
manager: erikre
ms.service: video-indexer
ms.topic: article
ms.date: 05/02/2017
ms.author: juliako;
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: a0d466258f6b5769d1a559506a9d0929ec6ba057
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="create-video-insights-from-existing-videos"></a>Create video insights from existing videos

This topic shows you how to create and publish video insights based on some other video.

1. Sign in to your [Video Indexer](https://www.vi.microsoft.com) account.
2. Find a video from which you want to create your video insights.
3. Press **Play**.

    The page shows the video's summarized insights. 

    ![Insights](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. Press the **Edit** button.

    This page shows you the full breakdown of a video. The breakdown is broken into blocks. Blocks are here to make it easier to go through the data. For example, block might be broken down based on when speakers change or there is a long pause. You can create your own playlist that contains only lines that you want. To show only specific parts of the source video, you can filter by topics/keywords, sentiments, people, speakers. You can choose to only view the video's transcript or OCR.    

    ![Insights](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. Create your playlist.

    To add or remove lines to/from your playlist, press **+**/**-**.

5. Preview your playlist.

    Once you are done creating the playlist, press **Preview**.
6. Publish the playlist.

    After you preview the playlist, you can publish it.

    Once you publish the playlist, it is added to the list of your video insights.


## <a name="next-steps"></a>Next steps 

Once you create the new playlist, you can continue processing it, as described in one of these topics: 

- [Process content with Video Indexer REST API](video-indexer-use-apis.md)
- [Embed visual widgets in your application](video-indexer-embed-widgets.md)

## <a name="see-also"></a>See also

[Video Indexer overview](video-indexer-overview.md) 

