<properties 
	pageTitle="Azure Media Services ジョブの管理" 
	description="このトピックでは、Azure Media Services ジョブの管理方法の概要を説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/27/2015" 
	ms.author="juliako"/>

##ジョブの管理

各**ジョブ**には、実行する処理に関するメタデータが含まれています。各**ジョブ**には、1 つ以上の**タスク**が含まれ、これらのタスクは、アトミック処理タスク、その入力アセット、出力アセット、メディア プロセッサとそれに関連付けられた設定を指定します。エンコーダーの設定の詳細については、エンコーダーに関するガイドおよびスキーマをご覧ください。

通常、エンコード ジョブは他の処理 (パッケージ化、またはエンコーダが生成した 1 つ以上のアセットの暗号化など) と結合されます。ジョブ内の複数のタスクは、1 つのタスクの出力アセットを次のタスクの入力アセットとして指定した場合、連結できます。この方法では、1 つのジョブにメディア表現に必要なすべての処理を含めることができます。


>[AZURE.NOTE]現時点では、ジョブごとに設定できるタスクは 30 までです。30 以上のタスクをつなげる必要がある場合、別のジョブを作成してタスクを設定します。

##ジョブ/タスクの操作

このセクションでは、ジョブやタスクを操作するときに実行する一般的なタスクを説明したリンクを提供します。

###メディア プロセッサの取得

メディア プロセッサを取得するには、**.NET** または **REST API** を使用します。

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

###ジョブの作成 

ジョブは、一連のタスク (エンコード、インデックス作成など) に関するメタデータを含むエンティティです。各タスクでは、入力アセットでアトミック操作が実行されます。エンコード ジョブの作成方法の例については、以下をご覧ください。

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###インデックス作成

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

###エンコード 

**Azure の管理ポータル**、**.NET**、**REST API** を使用した **Azure Media Encoder** でのエンコードについては、以下をご覧ください。
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###ジョブの進行状況の監視

**Azure の管理ポータル**、**.NET**、**REST API** を使用してジョブの進行状況を監視するには、以下をご覧ください。

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

##関連リンク

[Quotas and Limitations (クォータと制限)](../media-services-quotas-and-limitations) - Media Services Encoder で使用するクォータと制限について説明します。

<!--HONumber=47-->
