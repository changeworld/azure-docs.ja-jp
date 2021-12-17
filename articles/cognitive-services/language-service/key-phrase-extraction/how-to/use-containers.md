---
title: オンプレミスのキー フレーズ抽出に Docker コンテナーを使用する
titleSuffix: Azure Cognitive Services
description: オンプレミスのキー フレーズ抽出に Docker コンテナーを使用する方法を説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-key-phrase, ignite-fall-2021
keywords: オンプレミス, Docker, コンテナー, 自然言語処理
ms.openlocfilehash: 51d7c53beaeabe57df5fe817cdb5c16c6d1d4252
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092764"
---
# <a name="install-and-run-key-phrase-extraction-containers"></a>キー フレーズ抽出コンテナーをインストールして実行する


コンテナーを使うと、独自のインフラストラクチャで Key Phrase Extraction API をホストすることができます。 キー フレーズ抽出をリモートで呼び出すことでは満たすことができないセキュリティまたはデータ ガバナンスの要件がある場合は、コンテナーが適している可能性があります。


> [!NOTE]
> * 無料アカウントは 1 か月あたり 5,000 テキスト レコードに制限されており、**Free** および **Standard** [価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics)だけがコンテナーに対して有効です。 トランザクションの要求レートの詳細については、「[データ制限](call-api.md#data-limits)」を参照してください。

コンテナーを使用すると、独自の環境で Key Phrase Extraction API を実行でき、セキュリティとデータ ガバナンスの固有の要件に対応できます。 キー フレーズ抽出コンテナーでは、未加工のテキストに対する高度な自然言語処理が提供され、主要な機能として、感情分析、キー フレーズ抽出、言語検出の 3 つが含まれます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/cognitive-services/)を作成してください。
* ホスト コンピューターに [Docker](https://docs.docker.com/) がインストールされていること。 コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 
    * Windows では、Linux コンテナーをサポートするように Docker を構成することも必要です。
    * [Docker の概念](https://docs.docker.com/get-started/overview/)に関する基本的な知識が必要です。 
* Free (F0) または Standard (S) [価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)の<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="言語リソースの作成"  target="_blank">言語リソース</a>。

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>ホスト コンピューターの要件と推奨事項

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

次の表では、使用可能なキー フレーズ抽出コンテナーの最小仕様と推奨される仕様について説明します。 各 CPU コアは、少なくとも 2.6 ギガヘルツ (GHz) 以上である必要があります。 許容される 1 秒あたりのトランザクション数 (TPS) も示されています。

|  | 最小ホスト仕様 | 推奨されるホスト仕様 | 最小 TPS | 最大 TPS|
|---|---------|-------------|--|--|
| **キー フレーズ抽出**   | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |15 | 30| 

CPU コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して Microsoft Container Registry からコンテナー イメージをダウンロードします。

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```

[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーをホスト コンピューター上に準備できたら、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用してコンテナーを実行します。 コンテナーは一度実行すると、お客様が停止するまで動作し続けます。

> [!IMPORTANT]
> * 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
> * コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。
> * 感情分析コンテナーと言語検出コンテナーは API の v3 を使用し、一般公開されています。 v2 の API が使用されているキー フレーズ抽出のコンテナーは、プレビュー段階です。

"*キー フレーズ抽出*" コンテナーを実行するには、次の `docker run` コマンドを実行します。 次のプレースホルダーを実際の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | キー フレーズ抽出リソースのキー。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Key Phrase Extraction API にアクセスするためのエンドポイント。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *キー フレーズ抽出* コンテナーを実行します
* 1 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

[!INCLUDE [Running multiple containers on the same host](../../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。

コンテナーの API のホストとしては `http://localhost:5000` を使用します。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](../../concepts/configure-containers.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。

[!INCLUDE [Cognitive Services FAQ note](../../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>課金

キー フレーズ抽出コンテナーにより、Azure アカウントの "_キー フレーズ抽出_" リソースを使って、課金情報が Azure に送信されます。 

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]
\
## <a name="summary"></a>まとめ

この記事では、キー フレーズ抽出コンテナーの概念とそのダウンロード、インストール、実行のワークフローについて説明しました。 要約すると:

* キー フレーズ抽出により、Docker 用の Linux コンテナーが提供されます。
* コンテナー イメージは Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使って、コンテナーのホスト URI を指定することにより、キー フレーズ抽出コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 お客様のデータ (解析対象のテキストなど) が Cognitive Services コンテナーによって Microsoft に送信することはありません。

## <a name="next-steps"></a>次のステップ

* 構成設定については、[コンテナーの構成](../../concepts/configure-containers.md)に関するページを参照してください。
