---
title: " Azure ポータルを使用したストリーミング エンドポイントのスケール設定 | Microsoft Docs"
description: このチュートリアルでは、Azure ポータルを使用したストリーミング エンドポイントのスケール設定の手順について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: juliako

---
# Azure ポータルを使用したストリーミング エンドポイントのスケール設定
## Overview
> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。
> 
> 

クライアントに対するアダプティブ ビットレート ストリーミングでのビデオ配信は、Azure Media Services の代表的な用途の 1 つです。Media Services でサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH、HDS (Adobe PrimeTime/Access のライセンスが必要) です。

Media Services には動的パッケージ化機能があり、アダプティブ ビットレート MP4 でエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、HDS) でそのまますぐに配信することができます。つまり、事前にパッケージされたこれらのストリーミング形式のバージョンを保存しておく必要がありません。

動的パッケージ化機能を利用するには、次の作業が必要となります。

* メザニン (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルにエンコードする (エンコードの手順は、このチュートリアルで後ほど説明します)。
* コンテンツ配信元となる "*ストリーミング エンドポイント*" のストリーミング ユニットを少なくとも 1 つ作成する。以下の手順で、ストリーミング ユニットの数を変更する方法を示します。

動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて適切な応答を構築して返します。

さらに、ストリーミング ユニットを調整することで、ストリーミング エンドポイント サービスの容量を制御し、帯域幅の増化ニーズに対応できます。実稼働環境でのアプリケーションの 1 つ以上のスケール単位を割り当てることをお勧めします。ストリーミング ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できるほかに、[ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)、CDN 統合、高度な構成などの追加機能を利用できるようになります。詳細については、「[Azure ポータルを使用したストリーミング エンドポイントの管理](media-services-portal-manage-streaming-endpoints.md)」を参照してください。

## ストリーミング エンドポイントのスケール設定
ストリーミング予約ユニットを作成したり、数を変更したりするには、以下の手順を実行します。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. **[設定]** ウィンドウで **[ストリーミング エンドポイント]** をクリックします。
3. 次に、スケールを設定するストリーミング エンドポイントをクリックします。
4. スライダーを移動してストリーミング ユニットの数を指定します。

![ストリーミング エンドポイント](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

次の考慮事項が適用されます。

* 新しいストリーミング ユニットの割り当てが完了するまでには約 20 分かかります。
* 現在のところ、ストリーミング ユニットの数を正の値からゼロに戻すと、オンデマンド ストリーミングが最大 1 時間無効になります。
* コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。料金設定の詳細については、「[Azure 料金早見表](http://go.microsoft.com/fwlink/?LinkId=275107)」を参照してください。

## 次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0831_2016-->