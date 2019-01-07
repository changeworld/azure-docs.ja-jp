---
title: Video Indexer で人物モデルをカスタマイズする - Azure
titlesuffix: Azure Media Services
description: この記事では、Video Indexer の人物モデルと、そのカスタマイズ方法の概要を示します。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/05/2018
ms.author: anzaman
ms.openlocfilehash: 073cff22f17f496c2ff85cfbf716751dfea1e03e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283238"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Video Indexer で人物モデルをカスタマイズする


Video Indexer では、ビデオ コンテンツの顔検出と著名人認識がサポートされます。 著名人認識機能では、IMDB、Wikipedia、および LinkedIn のトップ インフルエンサーなど、一般的に要求されるデータ ソースに基づいて約 1,000,000 個の顔に対応します。 著名人認識機能で認識されない顔は、検出されますが、名前なしのままとなります。 Video Indexer にビデオをアップロードし、結果が戻ってきた後、前に戻り、認識されなかった顔に名前を付けることができます。 顔に名前でラベルを付けると、その顔と名前が、アカウントの人物モデルに追加されます。 その後、Video Indexer では、今後のビデオと過去のビデオでこの顔が認識されるようになります。

Video Indexer Web サイトまたは API を使用して、アカウント内のビデオで検出された顔を編集することができます。これについては、以下のトピックで説明します。

- [API を使用して人物モデルをカスタマイズする](customize-person-model-with-api.md)
- [Web サイトを使用して人物モデルをカスタマイズする](customize-person-model-with-website.md)
