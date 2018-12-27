---
title: HTTPS URL から Azure Media Services ジョブの入力を作成する | Microsoft Docs
description: このトピックでは、HTTP(S) URL から、ジョブの入力を作成する方法を示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: bac11640f5256223c1053f03da42c763508af98f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377380"
---
# <a name="create-a-job-input-from-an-https-url"></a>HTTPS URL からジョブの入力を作成する

Media Services v3 では、ビデオを処理するジョブを送信するときに、入力ビデオを検索する場所を Media Services に指示する必要があります。 選択肢の 1 つは、HTTP() URL をジョブの入力として指定することです (この記事で例を示します)。 現在、AMS v3では、HTTPS URLを介したチャンク転送のエンコーディングはサポートされていません。 完全な例については、こちらの [github サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)を参照してください。

## <a name="net-sample"></a>.Net のサンプル

次のコードは、HTTPS URL 入力を使用してジョブを作成する方法を示しています。

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>次の手順

[ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)。
