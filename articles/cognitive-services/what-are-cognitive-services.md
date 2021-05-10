---
title: Azure Cognitive Services とは
titleSuffix: Azure Cognitive Services
description: Cognitive Services は、すべての開発者にとって AI を身近なものにします。機械学習やデータサイエンスの専門知識は必要ありません。 必要なことは、アプリケーションから API を呼び出して、見たり (高度な画像検索と画像認識)、聞いたり、読み上げたり、検索したり、意思決定を行ったりする機能をアプリに追加するだけです。
services: cognitive-services
author: nitinme
manager: nitinme
keywords: Cognitive Services, コグニティブ インテリジェンス, コグニティブ ソリューション, AI サービス, コグニティブな理解, コグニティブ機能
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: c89131cc34d45ea94f3bb290ac11ec86f4b83be3
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587614"
---
# <a name="what-are-azure-cognitive-services"></a>Azure Cognitive Services とは

Azure Cognitive Services はクラウドベースのサービスです。REST API とクライアント ライブラリ SDK が用意されており、それらを利用することで、コグニティブなインテリジェンスを手軽にアプリケーションに組み込むことができます。 コグニティブ機能をアプリケーションに追加するために、人工知能 (AI) やデータ サイエンスのスキルは必要ありません。 Azure Cognitive Services を構成する各種の AI サービスを通じて、見る、聞く、話す、理解する、さらには、意思決定を行うといったことができるコグニティブ ソリューションを作成できます。

## <a name="categories-of-cognitive-services"></a>Cognitive Services のカテゴリ

コグニティブな理解を提供する Cognitive Services のカタログは、大きく次の 5 つに分けられます。

* 視覚
* 音声
* Language
* 決定
* 検索

以降この記事の各セクションでは、この 5 つの要素に含まれる一連のサービスについて取り上げます。

## <a name="vision-apis"></a>Vision API

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Computer Vision](./computer-vision/index.yml "Computer Vision")|Computer Vision サービスを使用すると、画像を処理して情報を返すための高度なコグニティブ アルゴリズムにアクセスできます。 サービスを開始するには、[Computer Vision のクイックスタート](./computer-vision/quickstarts-sdk/client-library.md)に関する記事をご覧ください。|
|[Custom Vision Service](./custom-vision-service/index.yml "Custom Vision Service")|Custom Vision Service を使用すると、独自の画像分類器を構築、デプロイ、改良できます。 画像分類器は、視覚特性に基づいて画像にラベルを適用する AI サービスです。 |
|[Face](./face/index.yml "Face")| Face サービスは、顔属性の検出と認識を有効にする、高度な顔アルゴリズムへのアクセスを提供します。 サービスを開始するには、[Face のクイックスタート](./face/quickstarts/client-libraries.md)に関する記事をご覧ください。|
|[Form Recognizer](./form-recognizer/index.yml "Form Recognizer")|Form Recognizer では、キーと値のペアおよびテーブル データを識別して form ドキュメントから抽出し、その後、元のファイルにあるリレーションシップを含む構造化データを出力します。 開始するには、[Form Recognizer のクイックスタート](./form-recognizer/quickstarts/client-library.md)に関する記事をご覧ください。|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|Video Indexer は、ビデオから洞察を抽出できます。 開始するには、[Video Indexer のクイックスタート](/azure/media-services/video-indexer/video-indexer-get-started)に関する記事をご覧ください。|

## <a name="speech-apis"></a>Speech API

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Speech サービス](./speech-service/index.yml "Speech サービス")|Speech サービスは、音声対応の機能をアプリケーションに追加します。 Speech サービスには、音声テキスト変換、テキスト読み上げ、音声翻訳など、さまざまな機能が含まれます。|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Language API

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Language Understanding LUIS](./luis/index.yml "Language Understanding")|Language Understanding (LUIS) は、カスタムの機械学習インテリジェンスをユーザーの自然言語での会話テキストに適用して、全体の意味を予測し、関連性のある詳細な情報を引き出す、クラウドベースの会話型 AI サービスです。 開始するには、[LUIS のクイックスタート](./luis/get-started-portal-build-app.md)に関する記事をご覧ください。|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|QnA Maker を使用すると、半構造化コンテンツから質疑応答サービスを構築できます。 開始するには、[QnA Maker のクイックスタート](./qnamaker/quickstarts/create-publish-knowledge-base.md)に関する記事をご覧ください。|
|[Text Analytics](./text-analytics/index.yml "Text Analytics")| Text Analytics によって、未加工のテキストに対して、感情分析、キー フレーズ抽出、および言語検出のための自然言語処理が提供されます。 開始するには、[Text Analytics のクイックスタート](./text-analytics/quickstarts/client-libraries-rest-api.md)に関する記事をご覧ください。|
|[Translator](./translator/index.yml "Translator")|Translator は、ほぼリアルタイムでのマシン ベースのテキスト翻訳を提供します。|
| [Immersive Reader](./immersive-reader/index.yml "イマーシブ リーダー") | Immersive Reader は、スクリーン リーダーや読解機能をアプリケーションに追加するものです。 サービスを開始するには、[イマーシブ リーダーのクイックスタート](./immersive-reader/quickstarts/client-libraries.md)をご覧ください。 |

## <a name="decision-apis"></a>Decision API

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Anomaly Detector](./anomaly-detector/index.yml "Anomaly Detector") |Anomaly Detector では、時系列データを監視し、その中の異常を検出できます。 開始するには、[Anomaly Detector のクイックスタート](./anomaly-detector/quickstarts/client-libraries.md)に関する記事をご覧ください。|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Content Moderator は、不快感を与えたり、望ましくなかったり、危険であったりする可能性があるコンテンツを監視します。 開始するには、[Content Moderator のクイックスタート](./content-moderator/client-libraries.md)に関する記事をご覧ください。|
|[Metrics Advisor](./metrics-advisor/index.yml) (プレビュー) | Metrics Advisor には、多変量の時系列データに対するカスタマイズ可能な異常検出機能と、そのサービスの使用を支援するあらゆる機能を備えた Web ポータルがあります。 開始するには、[Metrics Advisor のクイックスタート](./metrics-advisor/quickstarts/rest-api-and-client-library.md)に関する記事をご覧ください。 |
|[Personalizer](./personalizer/index.yml "Personalizer")|Personalizer は、ユーザーのリアルタイムの動作から学習し、ユーザーに表示する最良のエクスペリエンスを選択できるようにします。 開始するには、[Personalizer のクイックスタート](./personalizer/quickstart-personalizer-sdk.md)に関する記事をご覧ください。|

## <a name="search-apis"></a>検索 API

> [!NOTE]
> [Azure Cognitive Search](../search/index.yml) をお探しですか? 一部のタスクに Cognitive Services が使用されますが、他のシナリオをサポートする異なる検索テクノロジです。

|サービス名|サービスの説明|
|:-----------|:------------------|
|[Bing News Search](/azure/cognitive-services/bing-news-search/ "Bing News Search")|Bing News Search は、ユーザーのクエリに関連すると判断されたニュース記事の一覧を返します。|
|[Bing Video Search](/azure/cognitive-services/Bing-Video-Search/ "Bing Video Search")|Bing Video Search は、ユーザーのクエリに関連すると判断されたビデオの一覧を返します。|
|[Bing Web Search](./bing-web-search/index.yml "Bing Web Search")|Bing Web Search は、ユーザーのクエリに関連すると判断された検索結果の一覧を返します。|
|[Bing Autosuggest](/azure/cognitive-services/Bing-Autosuggest "Bing Autosuggest")|Bing Autosuggest は、部分的な検索クエリの用語を Bing に送信し、クエリ候補の一覧を取得できます。|
|[Bing Custom Search](/azure/cognitive-services/bing-custom-search "Bing Custom Search")|Bing Custom Search は、関心のあるトピックに合わせてカスタマイズした検索エクスペリエンスを作成できます。|
|[Bing Entity Search](/azure/cognitive-services/bing-entities-search/ "Bing Entity Search")|Bing Web Search では、ユーザーのクエリに関連があると Bing が判断したエンティティに関する情報が返されます。|
|[Bing Image Search](/azure/cognitive-services/bing-image-search "Bing Image Search")|Bing Image Search は、ユーザーのクエリに関連すると判断されたイメージの表示を返します。|
|[Bing Visual Search](/azure/cognitive-services/bing-visual-search "Bing Visual Search")|Bing Visual Search は、画像 (見た目が似ている画像、画像内にある製品のショッピング ソース、関連検索) に関する分析情報を返します。|
|[Bing Local Business Search](/azure/cognitive-services/bing-local-business-search/ "Bing Local Business Search")| Bing Local Business Search API を使用すると、アプリケーションでは検索クエリに基づいて地元企業に関する連絡先や場所の情報を検索できます。|
|[Bing Spell Check](/azure/cognitive-services/bing-spell-check/ "Bing Spell Check")|Bing Spell Check を使用して、コンテキストに応じた文法およびスペル チェックを実行できます。|

## <a name="get-started-with-cognitive-services"></a>Cognitive Services の概要

まず、以下の方法で Cognitive Services リソースを作成します。それぞれの方法については、ハンズオン クイックスタートをご覧ください。

* [Azure Portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure portal")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK クライアント ライブラリ](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Azure Resource Manager (ARM) テンプレート](./create-account-resource-manager-template.md?tabs=portal "Azure Resource Manager (ARM) テンプレート")

## <a name="using-cognitive-services-in-different-development-environments"></a>さまざまな開発環境で Cognitive Services を使用する

Azure と Cognitive Services を使用すると、いくつかの開発オプションを利用できます。以下はその例です。

* Logic Apps や Power Automate のような自動化および統合ツール。
* Azure Functions や App Service のようなデプロイ オプション。 
* セキュリティで保護されたアクセス用の Cognitive Services Docker コンテナー。
* ビッグ データ シナリオ用の Apache Spark、Azure Databricks、Azure Synapse Analytics、Azure Kubernetes Service などのツール。 

詳細については、「[Cognitive Services の開発オプション](./cognitive-services-development-options.md)」を参照してください。

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Cognitive Services を安全に使用する

Azure Cognitive Services には、[Azure Virtual Networks](cognitive-services-virtual-networks.md "Azure 仮想ネットワーク")、有効なリソース キー、Azure Active Directory の資格情報を使用した[認証](authentication.md "認証")を含む多層型のセキュリティ モデルが採用されています。

## <a name="containers-for-cognitive-services"></a>Cognitive Services のコンテナー

 Azure Cognitive Services には、Docker コンテナーがいくつか用意されており、これにより、Azure で使用できるものと同じ API をオンプレミスで使用できます。 これらのコンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由でデータに対して Cognitive Services を使用することが柔軟に可能になります。 詳細については、[Cognitive Services のコンテナー](cognitive-services-container-support.md "Cognitive Services コンテナー")に関するページを参照してください。

## <a name="regional-availability"></a>リージョン別の提供状況

Cognitive Services の API は、Microsoft マネージド データ センターの拡大しているネットワーク上でホストされます。 [Azure リージョン一覧](https://azure.microsoft.com/regions "Azure リージョンの一覧")で API ごとのリージョン別の提供状況がわかります。

まだサポートされていないリージョンを探しているのですか。 [UserVoice フォーラム](https://cognitive.uservoice.com/ "UserVoice フォーラム")に機能要求を記入してご連絡ください。

## <a name="supported-cultural-languages"></a>サポートされる言語 (カルチャ)

Cognitive Services では、さまざまなカルチャ言語がサービス レベルでサポートされています。 各 API で利用可能な言語は、[サポート対象の言語一覧](language-support.md "サポートされている言語の一覧")に関するページで検索できます。

## <a name="certifications-and-compliance"></a>認定資格とコンプライアンス

Cognitive Services には、CSA STAR Certification、FedRAMP Moderate、HIPAA BAA などの認定資格が与えられています。 自分で監査したり、セキュリティを確認する目的で認定資格を[ダウンロード](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "download")できます。

プライバシーやデータ管理は[セキュリティ センター](https://servicetrust.microsoft.com/ "トラスト センター")で確認できます。

## <a name="support"></a>サポート

Cognitive Services には、インテリジェント アプリケーションの作成を支援するいくつかのサポート オプションが用意されています。 また、個別の質問への回答を得ることができる強力な開発者コミュニティもあります。 提供されているオプションの全一覧については、[Cognitive Services のサポート オプションとヘルプ オプション](cognitive-services-support-options.md "Cognitive Services のサポート オプションとヘルプ オプション")に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [Cognitive Services アカウントを作成する](cognitive-services-apis-create-account.md "Cognitive Services アカウントを作成する")
* [Cognitive Services ドキュメントの新着情報](whats-new-docs.md "Cognitive Services ドキュメントの新着情報")
* [Cognitive Services のコストを計画および管理する](plan-manage-costs.md)
