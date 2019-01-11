---
title: Azure Media Services エンティティの概要 - Azure | Microsoft Docs
description: この記事では、Azure Media Services エンティティの概要を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/20/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 1d309bb550282d5245a2fbf74f14a931cf5c2279
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53745730"
---
# <a name="azure-media-services-entities-overview"></a>Azure Media Services エンティティの概要

この記事では、Azure Media Services エンティティの概要を手短に説明し、各エンティティの詳細に関する記事を指し示します。 

| トピック | 説明 |
|---|---|
| [アカウント フィルターとアセット フィルター](filters-dynamic-manifest-overview.md)|コンテンツを顧客に配信 (ライブ イベントやビデオ オン デマンドをストリーム配信) する際、資産の既定のマニフェスト ファイルに記述された内容だけではクライアントのニーズに柔軟に対応できない場合があります。 Azure Media Services では、[アカウント フィルター](https://docs.microsoft.com/rest/api/media/accountfilters)と[アセット フィルター](https://docs.microsoft.com/rest/api/media/assetfilters)を定義できます。 その後で、定義済みのフィルターに基づいて**動的マニフェスト**を使用します。 |
| [資産](assets-concept.md)|[アセット](https://docs.microsoft.com/rest/api/media/assets) エンティティには、デジタル ファイル (ビデオ、オーディオ、画像、サムネイルのコレクション、テキスト トラック、クローズド キャプション ファイルなど) と、それらのファイルに関するメタデータが含まれます。 デジタル ファイルは、アセットにアップロードされた後、Media Services エンコード、ストリーム配信、コンテンツ分析ワークフローで使用できます。|
| [コンテンツ キー ポリシー](content-key-policy-concept.md)|Media Services を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでのセキュリティ保護が可能になります。 Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム (Microsoft PlayReady、Google Widevine、および Apple FairPlay) によって動的に暗号化されたライブまたはオンデマンドのコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM (PlayReady、Widevine、FairPlay) ライセンスを配信するためのサービスも提供しています。|
| [LiveEvent と LiveOutput](live-events-outputs-concept.md)|Media Services では、Azure クラウドで顧客にライブ イベントを配信することができます。 Media Services v3 でライブ ストリーミング イベントを構成するには、[ライブ イベント](https://docs.microsoft.com/rest/api/media/liveevents)と[ライブ出力](https://docs.microsoft.com/rest/api/media/liveoutputs)について理解する必要があります。|
| [ストリーミング エンドポイント](streaming-endpoint-concept.md)|[ストリーミング エンドポイント](https://docs.microsoft.com/rest/api/media/streamingendpoints) エンティティは、クライアント プレーヤー アプリケーションや、以後の再配布のための Content Delivery Network (CDN) に、コンテンツを直接配信するストリーミング サービスを表します。 ストリーミング エンドポイント サービスからの送信ストリームには、Media Services アカウントのライブ ストリームやビデオ オンデマンドの資産があります。 Media Services アカウントを作成すると、**既定**のストリーミング エンドポイントが停止状態で作成されます。 **既定の**ストリーミング エンドポイントは削除できません。 アカウントで追加のストリーミング エンドポイントを作成できます。 ビデオのストリーミングを開始するには、ビデオをストリーミングするストリーミング エンドポイントを開始する必要があります。 |
| [ストリーミング ロケーター](streaming-locators-concept.md)|クライアントには、エンコードされたビデオまたはオーディオ ファイルを再生するために使用できる URL を提供する必要があり、[StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) を作成してストリーミング URL を構築する必要があります。|
| [ストリーミング ポリシー](streaming-policy-concept.md)| [ストリーミング ポリシー](https://docs.microsoft.com/rest/api/media/streamingpolicies)を使用して、StreamingLocator のためのストリーミング プロトコルと暗号化オプションを定義できます。 作成したカスタム ストリーミング ポリシーの名前を指定するか、Media Services によって提供されている、定義済みのストリーミング ポリシーのいずれかを指定できます。 <br/><br/>カスタム ストリーミング ポリシーの使用時には、お使いの Media Service アカウント用にこうしたポリシーの限られたセットを設計し、同じ暗号化オプションとプロトコルが必要な場合は常に、ストリーミング ロケーターに対して同じセットを再利用してください。 ストリーミング ロケーターごとに新しいストリーミング ポリシーを作成しないでください。|
| [Transform と Job](transforms-jobs-concept.md)|[Transform](https://docs.microsoft.com/rest/api/media/transforms) を使用して、ビデオのエンコードや分析を行うための一般的なタスクを構成できます。 各 **Transform** は、ビデオまたはオーディオ ファイルを処理するためのレシピ､すなわちタスクのワークフローの記述です｡<br/><br/>各 [Job](https://docs.microsoft.com/rest/api/media/jobs) は、与えられた入力ビデオまたはオーディオ コンテンツに **Transform** を適用する Azuru Media Services への実際の要求です。 **ジョブ** には、入力ビデオの場所や出力先などの情報を指定します。 入力ビデオの場所は、HTTPS URL、SAS URL、またはアセットを使用して指定することができます。|

## <a name="next-steps"></a>次の手順

[ファイルのストリーミング](stream-files-dotnet-quickstart.md)
