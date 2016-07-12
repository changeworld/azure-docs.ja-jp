<properties 
	pageTitle="Media Services の規模の設定方法" 
	description="アカウントにプロビジョニングする [オンデマンド ストリーミング占有ユニット] および [エンコード占有ユニット] の数を指定することでメディア サービスの規模を設定する方法について説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2016" 
	ms.author="juliako"/>


#Media Services の規模の設定方法  

##概要

**Media Services** は、アカウントのプロビジョニングに使用する **[ストリーミング占有ユニット]** と **[エンコード占有ユニット]** の数を指定することで規模を設定できます。

ストレージ アカウントを追加して、Media Services アカウントの規模を設定することもできます。各ストレージ アカウントの上限は 500 TB (テラバイト) です。既定の上限を超えるストレージ容量を設定するために、複数のストレージ アカウントを単一の Media Services アカウントにアタッチすることを選択できます。

このトピックは、関連するトピックにリンクしています。

##<a id="streaming_endpoins"></a>ストリーミング占有ユニット

詳細については、「[ストリーミング エンドポイントの拡張](media-services-manage-origins.md#scale_streaming_endpoints)」をご覧ください。

##<a id="encoding_reserved_units"></a>エンコード占有ユニット

エンコード ユニットの拡張の詳細については、**ポータル**と **.NET** のトピックをご覧ください。

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../../includes/media-services-selector-scale-encoding-units.md)]

予約ユニットは、エンコード タスクでもインデックス作成タスクでも同じです。

##<a id="storage"></a>ストレージのスケーリング

詳細については、「[複数のストレージ アカウントでの Media Services アセットの管理](https://msdn.microsoft.com/library/azure/dn271889.aspx)」や「[Azure ストレージの操作](https://msdn.microsoft.com/library/azure/dn767951.aspx)」をご覧ください。

##Media Services のラーニング パス

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##フィードバックの提供

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0629_2016-->