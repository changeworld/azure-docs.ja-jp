---
title: オンプレミスで言語検出 Docker コンテナーを使用する
titleSuffix: Azure Cognitive Services
description: オンプレミスで、Language Detection API 用の Docker コンテナーを使って、書き表されたテキストの言語を決定します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-language-detection, ignite-fall-2021
keywords: オンプレミス、Docker、コンテナー
ms.openlocfilehash: bd4dd685d1bfcc75f72eba5890562d9dd0685819
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091298"
---
# <a name="use-language-detection-docker-containers-on-premises"></a>オンプレミスで言語検出 Docker コンテナーを使用する 

コンテナーを使うと、独自のインフラストラクチャで Language Detection API をホストすることができます。 言語検出をリモートで呼び出すことでは満たすことができないセキュリティまたはデータ ガバナンスの要件がある場合は、コンテナーが適している可能性があります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/cognitive-services/)を作成してください。
* ホスト コンピューターに [Docker](https://docs.docker.com/) がインストールされていること。 コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 
    * Windows では、Linux コンテナーをサポートするように Docker を構成することも必要です。
    * [Docker の概念](https://docs.docker.com/get-started/overview/)に関する基本的な知識が必要です。 
* Free (F0) または Standard (S) [価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)の<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="言語リソースの作成"  target="_blank">言語リソース</a>。

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>ホスト コンピューターの要件と推奨事項

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

次の表では、言語検出コンテナーの最小仕様と推奨仕様を説明します。 各 CPU コアは、少なくとも 2.6 ギガヘルツ (GHz) 以上である必要があります。 許容される 1 秒あたりのトランザクション数 (TPS) も示されています。

|  | 最小ホスト仕様 | 推奨されるホスト仕様 | 最小 TPS | 最大 TPS|
|---|---------|-------------|--|--|
| **言語検出**   | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |15 | 30| 

CPU コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

Microsoft Container Registry からコンテナー イメージをダウンロードするには、[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使います。

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/language:latest
```

[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーをホスト コンピューター上に準備できたら、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用してコンテナーを実行します。 コンテナーは一度実行すると、お客様が停止するまで動作し続けます。

> [!IMPORTANT]
> * 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
> * コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

"*言語検出*" コンテナーを実行するには、次の `docker run` コマンドを実行します。 次のプレースホルダーを実際の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | 言語リソースのキー。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Language Detection API にアクセスするためのエンドポイント。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *Language Detection* コンテナーを実行します
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

言語検出コンテナーにより、Azure アカウントの "_言語_" リソースを使って、Azure に課金情報が送信されます。 

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](../../concepts/configure-containers.md)」を参照してください。

## <a name="summary"></a>まとめ

この記事では、言語検出コンテナーの概念とそのダウンロード、インストール、実行のワークフローについて説明しました。 要約すると:

* 言語検出により、Docker 用の Linux コンテナーが提供されます
* コンテナー イメージは Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> このコンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 お客様のデータ (解析対象のテキストなど) が Cognitive Services コンテナーによって Microsoft に送信することはありません。

## <a name="next-steps"></a>次のステップ

* 構成設定については、[コンテナーの構成](../../concepts/configure-containers.md)に関するページを参照してください。
