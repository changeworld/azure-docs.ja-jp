---
title: " Azure Portal を使用したストリーミング エンドポイントのスケール設定 | Microsoft Docs"
description: "このチュートリアルでは、Azure ポータルを使用したストリーミング エンドポイントのスケール設定の手順について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: ff663f40507547ba561053b5c9a7a8ce93fbf213
ms.openlocfilehash: f9f7872eab6b61afcbdc2d8eb23f1dc8ada82829


---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Azure ポータルを使用したストリーミング エンドポイントのスケール設定
## <a name="overview"></a>概要
> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
> 
> 

クライアントに対するアダプティブ ビットレート ストリーミングでのビデオ配信は、Azure Media Services の代表的な用途の&1; つです。 Media Services でサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、Smooth Streaming、および MPEG DASH です。

Media Services にはダイナミック パッケージ機能があり、アダプティブ ビットレート MP4 でエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、Smooth Streaming) でそのまますぐに配信できます。つまり、事前にパッケージされたこれらのストリーミング形式のバージョンを保存しておく必要がありません。

動的パッケージ化機能を利用するには、次の作業が必要となります。

* メザニン (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルにエンコードする (エンコードの手順は、このチュートリアルで後ほど説明します)。  
* コンテンツ配信元となる " *ストリーミング エンドポイント* " のストリーミング ユニットを少なくとも&1; つ作成する。 以下の手順で、ストリーミング ユニットの数を変更する方法を示します。

動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて適切な応答を構築して返します。

さらに、ストリーミング ユニットを調整することで、ストリーミング エンドポイント サービスの容量を制御し、帯域幅の増化ニーズに対応できます。 実稼働環境でのアプリケーションの&1; つ以上のスケール単位を割り当てることをお勧めします。 ストリーミング ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できるほかに、 [ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)、CDN 統合、高度な構成などの追加機能を利用できるようになります。 詳細については、「 [Azure ポータルを使用したストリーミング エンドポイントの管理](media-services-portal-manage-streaming-endpoints.md)」を参照してください。

## <a name="scale-streaming-endpoints"></a>ストリーミング エンドポイントのスケール設定
ストリーミング予約ユニットを作成したり、数を変更したりするには、以下の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** ウィンドウで **[ストリーミング エンドポイント]** を選択します。
3. 次に、スケールを設定するストリーミング エンドポイントをクリックします。 
4. スライダーを移動してストリーミング ユニットの数を指定します。

![ストリーミング エンドポイント](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

次の考慮事項が適用されます。

* 新しいストリーミング ユニットの割り当てが完了するまでには約 20 分かかります。 
* 現在のところ、ストリーミング ユニットの数を正の値からゼロに戻すと、オンデマンド ストリーミングが最大&1; 時間無効になります。
* コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。 料金設定の詳細については、「 [Azure 料金早見表](http://go.microsoft.com/fwlink/?LinkId=275107)」を参照してください。

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Dec16_HO2-->


