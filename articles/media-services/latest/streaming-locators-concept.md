---
title: Azure Media Services のストリーミング ロケーター | Microsoft Docs
description: この記事では、ストリーミング ロケーターとは何か、および Azure Media Services でそれらを使用する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 9a14399117971807c1d18f8eb5fab7d6e6cef2d5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120347"
---
# <a name="streaming-locators"></a>ストリーミング ロケーター

出力アセット内のビデオをクライアントが再生できるようにするには、[ストリーミング ロケーター](https://docs.microsoft.com/rest/api/media/streaminglocators)を作成し、ストリーミング URL をビルドする必要があります。 .NET のサンプルについては、「[Get a Streaming Locator](stream-files-tutorial-with-api.md#get-a-streaming-locator)」(ストリーミング ロケーターを取得する) を参照してください。

**ストリーミング ロケーター** を作成するプロセスは発行と呼ばれます。 既定では、**ストリーミング ロケーター** は API 呼び出しを行うとすぐに有効になり、省略可能な開始時刻と終了時刻を構成しない限り、削除されるまで存続します。 

**ストリーミング ロケーター**を作成するときに、**アセット**名と**ストリーミング ポリシー**名を指定する必要があります。 詳細については、次のトピックを参照してください。

* [資産](assets-concept.md)
* [ストリーミング ポリシー](streaming-policy-concept.md)
* [コンテンツ キー ポリシー](content-key-policy-concept.md)

> [!IMPORTANT]
> * Datetime 型の**ストリーミング ロケーター**のプロパティは、常に UTC 形式です。
> * お使いの Media Service アカウント用にポリシーの限られたセットを設計し、同じオプションが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 詳細については、「[クォータと制限](limits-quotas-constraints.md)」をご覧ください。

## <a name="associate-filters-with-streaming-locators"></a>フィルターをストリーミング ロケーターに関連付ける

「[フィルターをストリーミング ロケーターに関連付ける](filters-concept.md#associate-filters-with-streaming-locator)」を参照してください。

## <a name="filter-order-page-streaming-locator-entities"></a>ストリーミング ロケーター エンティティのフィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

[チュートリアル:.NET を使用してビデオをアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)
