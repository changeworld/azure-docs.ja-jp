---
title: 感情分析用の Docker コンテナーをインストールして実行する
titleSuffix: Azure Cognitive Services
description: Sentiment Analysis API 用の Docker コンテナーを使用し、感情分析などの自然言語処理をオンプレミスで実行します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
keywords: オンプレミス, Docker, コンテナー, 感情分析, 自然言語処理
ms.openlocfilehash: 0239280045cab18627ba6e888cd75b03de0d54e4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092236"
---
# <a name="install-and-run-sentiment-analysis-containers"></a>感情分析コンテナーをインストールして実行する

コンテナーを使うと、独自のインフラストラクチャで Sentiment Analysis API をホストすることができます。 感情分析をリモートで呼び出すことでは満たすことができないセキュリティまたはデータ ガバナンスの要件がある場合は、コンテナーが適している可能性があります。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

感情分析コンテナーを使用する前に、次の前提条件を満たす必要があります。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

* ホスト コンピューターに [Docker](https://docs.docker.com/) がインストールされていること。 コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 
    * Windows では、Linux コンテナーをサポートするように Docker を構成することも必要です。
    * [Docker の概念](https://docs.docker.com/get-started/overview/)に関する基本的な知識が必要です。 
* Free (F0) または Standard (S) [価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)の<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="言語リソースの作成"  target="_blank">言語リソース</a>。

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>ホスト コンピューターの要件と推奨事項

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

次の表では、利用できるコンテナーの最小仕様と推奨仕様を説明します。 各 CPU コアは、少なくとも 2.6 ギガヘルツ (GHz) 以上である必要があります。 許容される 1 秒あたりのトランザクション数 (TPS) も示されています。

|  | 最小ホスト仕様 | 推奨されるホスト仕様 | 最小 TPS | 最大 TPS|
|---|---------|-------------|--|--|
| **感情分析**   | 1 コア、2 GB メモリ | 4 コア、8 GB メモリ |15 | 30|

CPU コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

複数の言語で、感情分析コンテナー v3 のコンテナーを使用できます。 英語のコンテナー用のコンテナーをダウンロードするには、次のコマンドを使用します。 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

別の言語用のコンテナーをダウンロードするには、`en` を次のいずれかの言語コードに置き換えます。 

| 感情分析コンテナー | 言語コード |
|--|--|
| 簡体中国語    |   `zh-hans`   |
| 繁体字中国語   |   `zh-hant`   |
| オランダ語                 |     `nl`      |
| 英語               |     `en`      |
| フランス語                |     `fr`      |
| ドイツ語                |     `de`      |
| ヒンディー語                 |    `hi`       |
| イタリア語               |     `it`      |
| 日本語              |     `ja`      |
| 韓国語                |     `ko`      |
| ノルウェー語 (ブークモール)   |     `no`      |
| ポルトガル語 (ブラジル)   |    `pt-BR`    |
| ポルトガル語 (ポルトガル) |    `pt-PT`    |
| スペイン語               |     `es`      |
| トルコ語               |     `tr`      |

[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーをホスト コンピューター上に準備できたら、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用してコンテナーを実行します。 コンテナーは一度実行すると、お客様が停止するまで動作し続けます。

> [!IMPORTANT]
> * 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
> * コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

感情分析コンテナーを実行するには、次の `docker run` コマンドを実行します。 次のプレースホルダーを実際の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | 言語リソースのキー。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | API にアクセスするためのエンドポイント。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{LANGUAGE}** | 実行するコンテナーの言語。 これが使用した `docker pull` コマンドと一致することを確認します。 次の例で言語の前にハイフン (`-`) が使用されていることに注意してください。 | `en` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment-{LANGUAGE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *感情分析* コンテナーを実行します。
* 1 つの CPU コアと 8 ギガバイト (GB) のメモリを割り当てます。
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

感情分析コンテナーにより、Azure アカウントの "_言語_" リソースを使って、Azure に課金情報が送信されます。

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](../../concepts/configure-containers.md)」を参照してください。

## <a name="summary"></a>まとめ

この記事では、感情分析コンテナーの概念とそのダウンロード、インストール、実行のワークフローについて説明しました。 要約すると:

* 感情分析により、Docker 用の Linux コンテナーが提供されます
* コンテナー イメージは Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 お客様のデータ (解析対象のテキストなど) が Cognitive Services コンテナーによって Microsoft に送信することはありません。

## <a name="next-steps"></a>次のステップ

* 構成設定については、[コンテナーの構成](../../concepts/configure-containers.md)に関するページを参照してください。
