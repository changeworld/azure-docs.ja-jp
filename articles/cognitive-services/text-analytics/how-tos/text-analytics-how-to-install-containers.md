---
title: Text Analytics API 向けの Docker コンテナーをインストールし、実行する
titleSuffix: Azure Cognitive Services
description: Text Analytics API 用の Docker コンテナーを使用し、感情分析などの自然言語処理をオンプレミスで実行します。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020, devx-track-azurecli
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: aahi
keywords: オンプレミス, Docker, コンテナー, 感情分析, 自然言語処理
ms.openlocfilehash: a9ad2d04e51e4f3e2ba46a140eb516d449d90b76
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036571"
---
# <a name="install-and-run-text-analytics-containers"></a>Text Analytics コンテナーをインストールして実行する

コンテナーを使用すると、独自の環境で Text Analytic API を実行でき、セキュリティとデータ ガバナンスの固有の要件に対応できます。 次の Text Analytics コンテナーを使用できます。

* 感情分析
* 言語検出
* キー フレーズ抽出 (プレビュー)
* Text Analytics for Health 

> [!NOTE]
> * 現時点では、エンティティのリンクと NER をコンテナーとして使用することはできません。
> * コンテナー イメージの場所が最近変更された可能性があります。 このコンテナーの更新された場所については、この記事をご覧ください。
> * 無料アカウントは 1 か月あたり 5,000 テキスト レコードに制限されており、**Free** および **Standard** [価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics)だけがコンテナーに対して有効です。 トランザクションの要求レートの詳細については、「[データ制限](../concepts/data-limits.md)」を参照してください。

コンテナーを使用すると、独自の環境で Text Analytic API を実行でき、セキュリティとデータ ガバナンスの固有の要件に対応できます。 Text Analytics コンテナーは、未加工のテキストに対して高度な自然言語処理を提供し、主要な機能として、感情分析、キー フレーズ抽出、言語検出の 3 つを備えています。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

Text Analytics コンテナーを使用する前に、次の前提条件を満たす必要があります。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

* ホスト コンピューターに [Docker](https://docs.docker.com/) がインストールされていること。 コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 
    * Windows では、Linux コンテナーをサポートするように Docker を構成することも必要です。
    * [Docker の概念](https://docs.docker.com/get-started/overview/)に関する基本的な知識が必要です。 
* Free (F0) または Standard (S) [価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)の <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics リソースを作成する"  target="_blank">Text Analytics リソース</a>。

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>ホスト コンピューターの要件と推奨事項

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

次の表では、使用可能な Text Analytics コンテナーの最小仕様と推奨される仕様について説明します。 各 CPU コアは、少なくとも 2.6 ギガヘルツ (GHz) 以上である必要があります。 許容される 1 秒あたりのトランザクション数 (TPS) も示されています。

|  | 最小ホスト仕様 | 推奨されるホスト仕様 | 最小 TPS | 最大 TPS|
|---|---------|-------------|--|--|
| **言語検出**   | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |15 | 30| 
| **キー フレーズ抽出 (プレビュー)**   | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |15 | 30| 
| **感情分析**   | 1 コア、2 GB メモリ | 4 コア、8 GB メモリ |15 | 30|
| **Text Analytics for Health - 1 ドキュメント/要求**   |  4 コア、10 GB メモリ | 6 コア、12 GB メモリ |15 | 30|
| **Text Analytics for Health - 10 ドキュメント/要求**   |  6 コア、16 GB メモリ | 8 コア、20 GB メモリ |15 | 30|

CPU コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

# <a name="sentiment-analysis"></a>[感情分析](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[キー フレーズ抽出 (プレビュー)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[言語検出](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[Text Analytics for Health](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーをホスト コンピューター上に準備できたら、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用してコンテナーを実行します。 コンテナーは一度実行すると、お客様が停止するまで動作し続けます。

> [!IMPORTANT]
> * 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
> * コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。
>   * Text Analytics for Health コンテナーを使用している場合は、[責任ある AI](/legal/cognitive-services/text-analytics/transparency-note-health) (RAI) 確認も `accept` の値で存在する必要があります。
> * 感情分析コンテナーと言語検出コンテナーは API の v3 を使用し、一般公開されています。 v2 の API が使用されているキー フレーズ抽出のコンテナーは、プレビュー段階です。

# <a name="sentiment-analysis"></a>[感情分析](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[キー フレーズ抽出 (プレビュー)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[言語検出](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health"></a>[Text Analytics for Health](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。

コンテナーの API のホストとしては `http://localhost:5000` を使用します。

<!--  ## Validate container is running -->

[!INCLUDE [Container API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](../text-analytics-resource-container-config.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>課金

Text Analytics コンテナーは、Azure アカウントの _Text Analytics_ リソースを使用して、Azure に課金情報を送信します。 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](../text-analytics-resource-container-config.md)」を参照してください。

## <a name="summary"></a>まとめ

この記事では、Text Analytics コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて説明しました。 要約すると:

* Text Analytics は、Docker 用に 3 つの Linux コンテナーを提供し、さまざまな機能をカプセル化します。
   * *感情分析*
   * *キー フレーズ抽出 (プレビュー)* 
   * *言語検出*
   * *Text Analytics for Health*
* コンテナー イメージは Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、Text Analytics コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 お客様のデータ (解析対象のテキストなど) が Cognitive Services コンテナーによって Microsoft に送信することはありません。

## <a name="next-steps"></a>次のステップ

* 構成設定については、[コンテナーの構成](../text-analytics-resource-container-config.md)に関するページを参照してください。
