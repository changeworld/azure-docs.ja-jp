---
title: Azure Media Services v2 から v3 への移行 | Microsoft Docs
description: この記事では、Azure Media Services v3 で導入された変更点と、2 つのバージョンの違いについて説明します。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, ストリーム, ブロードキャスト, ライブ, オフライン
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 11/05/2018
ms.author: juliako
ms.openlocfilehash: 2f5c0ef63ba150fdad4aea1a0c65269611d56815
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247689"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Media Services v2 から v3 への移行のガイダンス

この記事では、Azure Media Services v3 で導入された変更点について説明し、2 つのバージョンの違いを示し、移行のガイダンスを提供します。

[従来の Media Services v2 API](../previous/media-services-overview.md) 上に動画サービスを今日開発した場合、v3 API に移行する前に次のガイドラインと 考慮事項を確認してください。 v3 API には Media Services の開発者エクスペリエンスと機能を向上させる多くのメリットおよび新機能があります。 ただし、この記事の[既知の問題](#known-issues)セクションで示すように、API バージョン間の変更によるいくつかの制限事項もあります。 このページは、v3 API に対して Media Services チームが継続的な改善を行い、バージョン間の差異に対処するたびに更新されます。 

## <a name="benefits-of-media-services-v3"></a>Media Services v3 のメリット

### <a name="api-is-more-approachable"></a>API がわかりやすい

*  v3 は、Azure Resource Manager 上に構築された管理と操作の両方の機能を公開する統一された API サーフェスに基づいています。 Azure Resource Manager テンプレートを使って、変換、ストリーミング エンドポイント、LiveEvent などを作成して展開できます。
* [Open API (別称 Swagger) 仕様](https://aka.ms/ams-v3-rest-sdk)ドキュメント。
    ファイル ベースのエンコードを含むすべてのサービス コンポーネントのスキーマを公開します。
* [.NET](https://aka.ms/ams-v3-dotnet-ref)、.Net Core、[Node.js](https://aka.ms/ams-v3-nodejs-ref)、[Python](https://aka.ms/ams-v3-python-ref)、[Java](https://aka.ms/ams-v3-java-ref)、[Go](https://aka.ms/ams-v3-go-ref)、およびRuby で利用できる SDK。
* 単純なスクリプト サポートのための [Azure CLI](https://aka.ms/ams-v3-cli-ref) 統合。

### <a name="new-features"></a>新機能

* ファイル ベースのジョブ処理では、入力として HTTP (S) URL を使用できます。
    Azure にコンテンツをあらかじめ格納しておく必要がなく、資産を作成する必要もありません。
* ファイル ベースのジョブ処理のための[変換](transforms-jobs-concept.md)の概念が導入されました。 変換を使用して、再利用可能な構成を構築し、Azure Resource Manager テンプレートを作成し、複数の顧客またはテナント間の処理の設定を分離することができます。
* 1 つの資産で、ダイナミック パッケージと動的暗号化の設定が異なる[複数の StreamingLocator](streaming-locators-concept.md) を持つことができます。
* [コンテンツ保護](content-key-policy-concept.md)はマルチキー機能をサポートしています。
* 最大 24 時間の長さのライブ イベントをストリーミングすることができます。
* LiveEvent に対する新しい低待機時間ライブ ストリーミング サポート。
* LiveEvent Preview は、ダイナミック パッケージと動的暗号化をサポートしています。 そのため、プレビューだけでなく、DASH および HLS パッケージに対するコンテンツ保護が可能です。
* LiveOuput は、v2 API の Program エンティティよりも使用方法が簡単です。 
* エンティティに対するロールベースのアクセス制御 (RBAC) があります。 

## <a name="changes-from-v2"></a>v2 からの変更点

* v3 で作成された資産の場合、Media Services は [Azure Storage サーバー側のストレージ暗号化](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)のみをサポートします。
    * V3 API は、[ストレージ暗号化](../previous/media-services-rest-storage-encryption.md) (AES 256) が Media Services によって提供された v2 API で作成された資産と一緒に使用できます。
    * v3 API を使用して従来の AES 256 [ストレージ暗号化](../previous/media-services-rest-storage-encryption.md)で新しい資産を作成することはできません。
* v3 SDK が Storage SDK から分離されたため、使用する Storage SDK のバージョンをより詳細に制御し、バージョン管理の問題を回避できるようになりました。 
* v3 API では、エンコード ビット レートはすべてビット/秒単位です。 これは v2 Media Encoder Standard のプリセットとは異なります。 たとえば、v2 のビットレートは 128 (kbps) と指定されていますが、v3 では 128,000 (ビット/秒) です。 
* v3 にはエンティティ AssetFiles、AccessPolicies、IngestManifests が存在しません。
* ContentKeys はエンティティではなくなり、StreamingLocator のプロパティです。
* Event Grid のサポートによって NotificationEndpoints を置き換えられました。
* 次のエンティティ名が変更されました
    * JobOutput は Task に置き換えられ、ジョブの一部になりました。
    * StreamingLocator によって Locator が置き換えられました。
    * LiveEvent によって Channel が置き換えられました。
        
        LiveEvent 課金はライブ チャンネルの測定に基づいています。 詳細については、[ライブ ストリーミングの概要](live-streaming-overview.md#billing)および[価格](https://azure.microsoft.com/pricing/details/media-services/)に関するページを参照してください。
    * LiveOutput によって Program が置き換えられました。
* LiveOutput を明示的に開始する必要はなく、これらは作成時に起動され、削除されたときに停止します。 v2 API ではプログラムの動作方法が異なり、作成後に起動される必要がありました。

## <a name="feature-gaps-with-respect-to-v2-apis"></a>v2 API に関する機能のギャップ

v3 API には v2 API に関して次の機能ギャップがあります。 ギャップを埋めることは進行中の作業です。

* [Premium Encoder](../previous/media-services-premium-workflow-encoder-formats.md) と従来の [Media Analytics プロセッサ](../previous/media-services-analytics-overview.md)(Azure Media Services Indexer 2 プレビュー、Face Redactor など) は v3 を使用してアクセスできません。

    Media Indexer 1 または2 のプレビューからの移行を希望するお客様は、v3 API でプリセットされている AudioAnalyzer をすぐに使用できます。  この新しいプリセットには、古い Media Indexer 1 または 2 より多くの機能が含まれています。 

* 次に示すような v2 API の Media Encoder Standard の多くの高度な機能は v3 で現在利用できません。
    * (オンデマンド シナリオおよびライブ シナリオ用の) クリッピング
    * 資産の結合
    * オーバーレイ
    * トリミング
    * サムネイル スプライト
* コード変換を使用する LiveEvent は、ストリーム中のスレート挿入、カスタム プリセット、または API 呼び出しによる Ad マーカー挿入を現在サポートしていません。 

> [!NOTE]
> この記事にブックマークを設定し、更新がないか随時確認してください。

## <a name="code-differences"></a>コードの違い

次の表では、一般的なシナリオでの v2 と v3 のコードの違いを示します。

|シナリオ|V2 API|V3 API|
|---|---|---|
|資産の作成とファイルのアップロード |[v2 .NET の例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET の例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|ジョブの送信|[v2 .NET の例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET の例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>まず変換を作成し、次にジョブを送信する方法を示しています。|
|AES 暗号化を使用して資産を公開する |1.ContentKeyAuthorizationPolicyOption を作成します<br/>2.ContentKeyAuthorizationPolicy を作成します<br/>手順 3.AssetDeliveryPolicy を作成します<br/>4.資産を作成し、コンテンツをアップロードするか、ジョブを送信して出力資産を使用します<br/>5.AssetDeliveryPolicy を資産に関連付けます<br/>6.ContentKey を作成します<br/>7.ContentKey を資産にアタッチします<br/>8.AccessPolicy を作成します<br/>9.ロケーターを作成します<br/><br/>[v2 .NET の例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1.コンテンツ キー ポリシーを作成します<br/>2.資産を作成します<br/>手順 3.コンテンツをアップロードするか、資産を JobOutput として使用します<br/>4.StreamingLocator を作成する<br/><br/>[v3 .NET の例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|

## <a name="known-issues"></a>既知の問題

* 現時点では、Azure portal を使用して v3 リソースを管理することはできません。 [REST API](https://aka.ms/ams-v3-rest-sdk)、CLI、またはサポートされている SDK のいずれかを使用します。
* 今日では、メディア占有ユニットは Media Services v2 API を使用してのみ管理できます。 詳細については、[メディア処理のスケール設定](../previous/media-services-scale-media-processing-overview.md)に関するページを参照してください。
* v3 API を使用して作成された Media Services エンティティは v2 API で管理できません。  
* v2 API で作成されたエンティティを v3 API 経由で管理することは推奨されません。 2 つのバージョンのエンティティの違いによって互換性がなくなる例を次に示します。   
    * v2 で作成されたジョブと タスクは変換に関連付けられていないため、v3 で表示されません。 v3 の変換およびジョブに切り替えることをお勧めします。 切り替え中は、処理中の v2 ジョブを比較的短い期間でモニターする必要があります。
    * v2 で作成されたチャネルとプログラム (v3 で LiveEvents と LiveOutputs にマップされる) は、v3 での管理を続行できません。 便利なチャネル停止で v3 LiveEvent および LiveOutput に切り替えることをお勧めします。
    
        現時点では、実行を継続中のチャネルを移行することはできません。  
> [!NOTE]
> この記事にブックマークを設定し、更新がないか随時確認してください。

## <a name="next-steps"></a>次の手順

ビデオ ファイルのエンコードおよびストリーミングを簡単に始める方法については、[ファイルのストリーム配信](stream-files-dotnet-quickstart.md)に関するページをご覧ください。 

