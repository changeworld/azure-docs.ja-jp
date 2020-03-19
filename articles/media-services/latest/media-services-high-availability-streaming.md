---
title: Azure Media Services の高可用性のストリーミング
description: リージョンのデータセンターの停止や障害が発生した場合に、セカンダリ Media Services アカウントにフェールオーバーする方法について説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898989"
---
# <a name="media-services-high-availability-streaming"></a>Media Services の高可用性ストリーミング

現在、リージョンのデータセンターが停止した場合、基となるコンポーネントや依存するサービスに障害が発生した場合、Azure Media Services にはサービスの即時フェールオーバー機能がありません。 この記事では、ビデオオンデマンドのリージョン間ストリーミングを構築する方法について説明します。

## <a name="prerequisites"></a>前提条件

「[リージョン間のエンコード システムを構築する方法](media-services-high-availability-encoding.md)」を確認する

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>ビデオオンデマンドのリージョン間ストリーミングを構築する方法 

* ビデオオンデマンドのリージョン間ストリーミングには、[アセット](assets-concept.md)、[コンテンツ キー ポリシー](content-key-policy-concept.md) (使用する場合)、[ストリーミング ポリシー](streaming-policy-concept.md)、および[ストリーミング ロケーター](streaming-locators-concept.md)の複製が必要です。 
* 両方のリージョンでポリシーを作成し、最新の状態に保つ必要があります。 
* ストリーミング ロケーターを作成する場合は、同じロケーター ID 値、ContentKey ID 値、および ContentKey 値を使用する必要があります。  
* コンテンツをエンコードする場合は、リージョン A のコンテンツをエンコードして発行し、エンコードされたコンテンツをリージョン B にコピーし、リージョン A と同じ値を使用して発行することをお勧めします。
* 発行元とキー配信サービスのホスト名に対してトラフィック マネージャーを使用することができます (Media Services 構成では、これはカスタム キー サーバーの URL のようになります)。

## <a name="next-steps"></a>次のステップ

次のページを確認してください。

* [チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する](stream-files-dotnet-quickstart.md)
* [コード サンプル](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
