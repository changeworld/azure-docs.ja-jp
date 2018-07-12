---
title: Azure Media Services v3 の概要 | Microsoft Docs
description: この記事では、Media Services の概要を説明し、詳細についての記事へのリンクを示します。
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: Azure Media Services, ストリーム, ブロードキャスト, ライブ, オフライン
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 06/14/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 5205a6746f6a698768a60375e2e77db9cb535a71
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971909"
---
# <a name="what-is-azure-media-services-v3"></a>Azure Media Services v3 の概要

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [バージョン 2 - 一般公開](../previous/media-services-overview.md)
> * [バージョン 3 - プレビュー](media-services-overview.md)

> [!NOTE]
> Azure Media Services の最新バージョンはプレビュー段階であり、v3 と呼ばれることがあります。

クラウドベースのプラットフォームである Azure Media Services では、ブロードキャスト品質のビデオ ストリーミングを実現し、アクセス性と配信を強化し、コンテンツを分析するソリューションを構築できます。 アプリケーション開発者、コール センター、政府機関、エンターテイメント会社のいずれであっても、Media Services を利用すると、今日の最も一般的なモバイル デバイスとブラウザーの多くのユーザーに、優れた品質のメディア エクスペリエンスを提供するアプリケーションを作成できます。 

## <a name="what-can-i-do-with-media-services"></a>Media Services の機能

Media Services を使うと、クラウドでさまざまなメディア ワークフローを構築できます。Media Services で実現できることの例を次に示します。  

* 広範なブラウザーやデバイスで再生できるように、さまざまな形式のビデオを提供します。 さまざまなクライアント (モバイル デバイス、TV、PC など) にオンデマンドとライブ ストリーミングの両方を提供するには、ビデオ コンテンツとオーディオ コンテンツを適切にエンコードしてパッケージ化する必要があります。 このようなコンテンツを配信およびストリーミングする方法については、[ファイルのエンコードとストリーミングに関するクイック スタート](stream-files-dotnet-quickstart.md)をご覧ください。
* サッカー、野球、大学や高校のスポーツなど、多数のオンライン視聴者にライブ スポーツ イベントをストリーム配信します。 
* タウンホール ミーティング、市議会、立法機関などの公開の会合やイベントをブロードキャストします。
* 録画されたビデオやオーディオ コンテンツを分析します。 たとえば、より高い顧客満足度を実現するため、音声からテキストを抽出して、検索インデックスやダッシュボードを作成できます。 これにより、一般的な苦情、苦情の原因、その他の関連データに関する知見を引き出すことができます。 
* 顧客 (たとえば映画スタジオ) が独自の著作権所有作品のアクセスや使用を制限する必要がある場合は、サブスクリプション ビデオ サービスを作成して、DRM で保護されたコンテンツをストリーム配信します。
* 飛行機、列車、自動車で再生するためのオフライン コンテンツを提供します。 顧客は、ネットワークから切断される可能性があるときは、携帯電話やタブレットにコンテンツをダウンロードして再生する必要があります。
* より広範な視聴者 (たとえば、聴覚障碍を持つ人や、別の言語で読みたい人など) に対応できるよう、ビデオに字幕とキャプションを追加します。 
* 音声からのテキスト キャプション作成や、多言語への翻訳などのために、Azure Media Services と [Azure Cognitive Services APIs](https://docs.microsoft.com/azure/#pivot=products&panel=ai) で教育用 E ラーニング ビデオ プラットフォームを実装します。
* 瞬間的高負荷 (製品発表イベントの開始時など) を処理しやすくする大規模なスケーリングを Azure CDN が実現できるようにします。 

## <a name="v3-capabilities"></a>v3 の機能

v3 は、Azure Resource Manager 上に構築された管理と操作の両方の機能を公開する統一された API サーフェスに基づいています。 

このバージョンでは次の機能が提供されます。  

* メディア処理タスクまたは分析タスクの簡単なワークフローを定義するのに役立つ**変換**。 変換は、ビデオ ファイルとオーディオ ファイルを処理するためのレシピです。 変換にジョブを送信することで、変換を繰り返し適用して、コンテンツ ライブラリ内のすべてのファイルを処理できます。
* ビデオを処理 (エンコードまたは分析) するための**ジョブ**。 HTTP(S) URL、SAS URL、または Azure Blob Storage 内に存在するファイルへのパスを使って、ジョブで入力コンテンツを指定できます。 
* ジョブの進行状況や状態、またはライブ チャネルの開始/停止とエラー イベントを監視する**通知**。 通知は、Azure Event Grid の通知システムに統合されています。 Azure Media Services の複数のリソースでのイベントを簡単にサブスクライブすることができます。 
* **Azure Resource Management** テンプレートを使って、変換、ストリーミング エンドポイント、チャネル、その他を作成して展開できます。
* **ロールベースのアクセス制御**をリソース レベルで設定でき、変換やチャネルなどの特定のリソースへのアクセスをロックダウンできます。
* 複数の言語 (.NET、.NET Core、Python、Go、Java、Node.js) での**クライアント SDK**。

## <a name="naming-conventions"></a>名前付け規則

Azure Media Services v3 のリソース名 (アセット、ジョブ、変換など) には、Azure Resource Manager の名前付け規則が適用されます。 Azure Resource Manager に従って、リソース名は常に一意となります。 そのため、リソース名には一意識別子の文字列 (GUID など) を使用できます。 

Media Services リソース名には、"<",">"、"%"、"&"、":"、"&#92;"、"?"、"/"、"*"、"+"、"."、一重引用符などの制御文字を使用することができません。 それ以外の文字は使用できます。 リソース名の最大文字数は 260 文字です。 

Azure Resource Manager の名前付けの詳細については、[名前付けの要件](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)に関するページおよび「[名前付け規則](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)」を参照してください。

## <a name="how-can-i-get-started-with-v3"></a>v3 の利用を始める方法

開発者は、Media Services の [REST API](https://go.microsoft.com/fwlink/p/?linkid=873030) または REST API と対話できるクライアント ライブラリを使って、カスタム メディア ワークフローを簡単に作成、管理、メンテナンスできます。 REST Postman の例については、[こちら](https://github.com/Azure-Samples/media-services-v3-rest-postman)をご覧ください。 [Azure Resource Manager ベースの REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates) を使用することもできます。

Microsoft は、次のクライアント ライブラリを生成してサポートします。 

|クライアント ライブラリ|サンプル|
|---|---|
|[Azure CLI SDK](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)|[Azure CLI のサンプル](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)|
|[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)|[.NET のサンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)|
|[.NET Core SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0) (**[.NET CLI]** タブを選択します)|[.NET Core のサンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/mediaservices)||
|[Node.js SDK](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/index?view=azure-node-latest)|[Node.js のサンプル](https://github.com/Azure-Samples/media-services-v3-node-tutorials)|
|[Python SDK](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)||
|[Go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)||

Media Services が提供する [Swagger ファイル](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media)を使って、好みの言語/技術に対する SDK を生成できます。  

## <a name="next-steps"></a>次の手順

ビデオ ファイルのエンコードおよびストリーミングを簡単に始める方法については、[ファイルのストリーム配信](stream-files-dotnet-quickstart.md)に関するページをご覧ください。 

