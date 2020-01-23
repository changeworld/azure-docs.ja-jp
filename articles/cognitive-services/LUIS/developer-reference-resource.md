---
title: 開発者向けリソース - Language Understanding
titleSuffix: Azure Cognitive Services
description: 開発者には、Language Understanding 用の REST API と SDK の両方があります。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: a05bfcf5da2dc9336d04668b09b82038b00caf9d
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76168761"
---
# <a name="developer-resources-for-language-understanding"></a>Language Understanding の開発者向けリソース

開発者は、Language Understanding 用の REST API と SDK の両方を使用できます。

## <a name="azure-resource-management"></a>Azure Resource Management

Azure Cognitive Services 管理レイヤーを使用して、Language Understanding または Cognitive Service リソースの作成、編集、一覧表示、削除を行います。

ツールに基づくリファレンス ドキュメントを検索します。

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding の作成と予測要求

Language Understanding サービスには、作成する必要がある Azure リソースからアクセスします。 作成と予測のエンドポイント リソースという 2 つのリソースがあります。 これらのどちらのリソースでも、LUIS リソースを制御できます。

V3 予測エンドポイントの詳細については[こちら](luis-migration-api-v3.md)を参照してください。

### <a name="rest-apis"></a>REST API

作成と予測の両方のエンドポイント API は、REST API から入手できます。

|種類|Version|
|--|--|
|Authoring|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[プレビュー V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|予測|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>言語ベースの SDK

|言語 |リファレンス ドキュメント|Package|サンプル|クイックスタート|
|--|--|--|--|--|
|C#|[作成](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[予測](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet の作成](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet の予測](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.NET SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[アプリの作成と管理](sdk-authoring.md?pivots=programming-language-csharp)<br>[予測エンドポイントに対するクエリの実行](sdk-query-prediction-endpoint.md)|
|Go|[作成と予測](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[作成](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[予測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[REST を使用した作成と予測](luis-get-started-get-intent-from-rest.md)|
|Java|[作成と予測](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven の作成](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven の予測](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[作成](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[予測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[作成と予測](luis-get-started-get-intent-from-rest.md)
|Node.js|[作成](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[予測](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM の作成](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM の予測](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[作成](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[予測](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[REST を使用した作成と予測](luis-get-started-get-intent-from-rest.md)|
|Python|[作成と予測](sdk-authoring.md?pivots=programming-language-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[作成](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[作成](sdk-authoring.md?pivots=programming-language-python)<br>[REST を使用した予測](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>Containers

Language Understanding (LUIS) には、アプリのオンプレミス バージョンと包含バージョンを提供するための[コンテナー](luis-container-howto.md)が用意されています。

### <a name="export-and-import-formats"></a>エクスポートとインポートの形式

Language Understanding には、アプリケーションとそのモデルを JSON 形式、`.LU` ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) 形式、および Language Understanding コンテナー用の圧縮パッケージで管理する機能が用意されています。

これらの形式のインポートとエクスポートは、API と LUIS ポータルから利用できます。 ポータルでは、アプリの一覧とバージョンの一覧の一部としてインポートとエクスポートが提供されています。

## <a name="other-tools-and-sdks"></a>その他のツールと SDK

Bot Framework は、さまざまな言語の [SDK](https://github.com/Microsoft/botframework) として、[Azure Bot Service](https://dev.botframework.com/) を使用するサービスとして使用できます。

Bot Framework には、次のような Language Understanding に役立つ[いくつかのツール](https://github.com/microsoft/botbuilder-tools)が用意されています。

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) - マークダウン ファイルを使用して LUIS Language Understanding モデルを構築します
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) - LUIS.ai アプリケーションを作成および管理します
* [Dispatch](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch) - 親アプリと子アプリを管理します
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) - LUIS の意図とエンティティのバッキング C#/Typescript クラスを自動生成します。
* [ボット エミュレーター](https://github.com/Microsoft/BotFramework-Emulator/releases) - ボット開発者が Bot Framework SDK を使用して構築されたボットをテストおよびデバッグできるデスクトップ アプリケーションです


## <a name="next-steps"></a>次のステップ

* 一般的な [HTTP エラー コード](luis-reference-response-codes.md)について学習します。
* すべての API と SDK の[リファレンス ドキュメント](https://docs.microsoft.com/azure/index#pivot=sdkstools)
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) と [Azure Bot Service](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [コグニティブ コンテナー](../cognitive-services-container-support.md)