---
title: コンテナーのインストールと実行
titleSuffix: Text Analytics -  Azure Cognitive Services
description: このチュートリアルでの Text Analytics のコンテナーのダウンロード、インストール、および実行方法。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 11798c3bfd4032ad10c738032a816a2a0488ce67
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090535"
---
# <a name="install-and-run-containers"></a>コンテナーのインストールと実行

コンテナー化とは、アプリケーションまたはサービスをコンテナー イメージとしてパッケージ化するソフトウェア配布のアプローチです。 アプリケーションまたはサービスの構成と依存関係がコンテナー イメージに含まれます。 コンテナー イメージは、ほとんどまたはまったく変更せずにコンテナー ホストにデプロイできます。 コンテナーは、相互および基になるオペレーティング システムから分離され、仮想マシンよりもフット プリントが小さくなります。 短期間のタスクに対してコンテナーをコンテナー イメージからインスタンス化し、不要になったら削除できます。

Text Analytics では、次に示す一連の Docker コンテナーが提供され、それぞれに機能のサブセットが含まれています。

| コンテナー| 説明 |
|----------|-------------|
|キー フレーズ抽出 | 主なポイントを識別するキー フレーズを抽出します。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、この API は話題の中心として "食べ物" と "すばらしいスタッフ" を返します。 |
|言語検出 | 最大 120 の言語について、入力テキストが記述されている言語を検出してレポートします。 このコンテナーは、要求に含まれるドキュメントごとに 1 つの言語コードをレポートします。 言語コードは、評価値の強度を示すスコアと組みになります。 |
|感情分析 | 肯定的または否定的な感情の手がかりを探して未加工のテキストを分析します。 この API はドキュメントごとに 0 から 1 までの感情スコアを返します。1 が最も肯定的となります。 分析モデルは、広範囲にわたるテキスト本文と Microsoft の自然言語技術を利用して事前トレーニングされています。 [一部の言語](../language-support.md)については、この API はユーザーが指定したあらゆる未加工テキストを分析し、評価し、呼び出し元のアプリケーションに結果を直接返すことができます。 |

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="preparation"></a>準備

Text Analytics コンテナーを使用する前に、次の前提条件を満たす必要があります。

**Docker エンジン**: Docker エンジンをローカルにインストールしている必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms)、[Windows](https://docs.docker.com/docker-for-windows/) 上で Docker 環境を構成するパッケージが用意されています。 Windows では、Linux コンテナーをサポートするように Docker を構成する必要があります。 Docker コンテナーは、[Azure Kubernetes Service](/azure/aks/)、[Azure Container Instances](/azure/container-instances/)、または [Azure Stack](/azure/azure-stack/) にデプロイされた [Kubernetes](https://kubernetes.io/) クラスターに直接デプロイすることもできます。 Kubernetes を Azure Stack にデプロイする方法の詳細については、「[Kubernetes を Azure Stack にデプロイする](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)」を参照してください。

コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。

**Microsoft Container Registry と Docker に関する知識**: レジストリ、リポジトリ、コンテナー、コンテナー イメージなどの Microsoft Container Registry と Docker の両方の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。  

Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各 Text Analytics コンテナーに割り当てる CPU コア (2.6 GHz (ギガヘルツ) 以上) とメモリ (GB 単位) の最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
|キー フレーズ抽出 | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |
|言語検出 | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |
|感情分析 | 1 コア、2 GB メモリ | 1 コア、4 GB メモリ |

## <a name="download-container-images-from-microsoft-container-registry"></a>Microsoft コンテナー レジストリからのコンテナー イメージのダウンロード

Text Analytics のコンテナー イメージは Microsoft コンテナー レジストリから入手できます。 次の表に、Text Analytics コンテナーについて Microsoft コンテナー レジストリで使用可能なリポジトリを示します。 コンテナーをローカルで実行するためには、各リポジトリに含まれるコンテナー イメージをダウンロードする必要があります。

| コンテナー | リポジトリ |
|-----------|------------|
|キー フレーズ抽出 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|言語検出 | `mcr.microsoft.com/azure-cognitive-services/language` |
|感情分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

リポジトリからコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用します。 たとえば、リポジトリから最新のキー フレーズ抽出コンテナー イメージをダウンロードするには、次のコマンドを使用します。

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

Text Analytics コンテナーで使用可能なタグの詳しい説明については、Docker Hub の次のコンテナーを参照してください。

* [キー フレーズ抽出](https://go.microsoft.com/fwlink/?linkid=2018757)
* [言語検出](https://go.microsoft.com/fwlink/?linkid=2018759)
* [感情分析](https://go.microsoft.com/fwlink/?linkid=2018654)

> [!TIP]
> [docker images](https://docs.docker.com/engine/reference/commandline/images/) コマンドを使用して、ダウンロードしたコンテナー イメージを一覧表示できます。 たとえば、次のコマンドは、ダウンロードした各コンテナー イメージの ID、リポジトリ、およびタグが表として書式設定されて表示されます。
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>ダウンロードしたコンテナー イメージからのコンテナーのインスタンス化

ダウンロードしたコンテナー イメージからコンテナーをインスタンス化するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 たとえば、次のコマンドでは次の処理が行われます。

* 感情分析コンテナー イメージからコンテナーをインスタンス化します。
* 1 つの CPU コアと 8 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-tty を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789

```

> [!IMPORTANT]
> コンテナーをインスタンス化するには、`Eula`、`Billing`、および `ApiKey` コマンドライン オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

インスタンス化したら、コンテナーのホスト URI を使用して、コンテナーから操作を呼び出すことができます。 たとえば、次のホスト URI は、前の例でインスタンス化された感情分析コンテナーを表しています。

```http
http://localhost:5000/
```

> [!TIP]
> インスタンス化されたコンテナーの `/swagger` 相対 URI から、そのコンテナーによってサポートされる操作について説明する [OpenAPI 仕様](https://swagger.io/docs/specification/about/) (以前の Swagger 仕様) にアクセスできます。 たとえば、次の URI から、前の例でインスタンス化された感情分析コンテナーの OpenAPI 仕様にアクセスできます。
>
>  ```http
>  http://localhost:5000/swagger
>  ```

コンテナーから使用可能な [REST API 操作を呼び出す](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-call-api)か、または [Azure Cognitive Services Text Analytics クライアント ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)のクライアント ライブラリを使用して、それらの操作を呼び出すことができます。  
> [!IMPORTANT]
> コンテナーでクライアント ライブラリを使用する場合は、Cognitive Services Text Analytics SDK バージョン 2.1.0 以降が必要です。

コンテナーから特定の操作を呼び出すことと、Azure の対応するサービスから同じ操作を呼び出すことの違いは、操作を呼び出すために、Azure リージョンのホスト URI ではなく、コンテナーのホスト URI を使用することだけです。 たとえば、米国西部 Azure リージョンで実行されている Text Analytics インスタンスを使用する場合は、次の REST API 操作を呼び出します。

```http
POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
```

ローカル コンピューターで、実行されているキー フレーズ抽出コンテナーをその既定の構成で使用する場合は、次の REST API 操作を呼び出します。

```http
POST http://localhost:5000/text/analytics/v2.0/keyPhrases
```

### <a name="billing"></a>課金

Text Analytics コンテナーは、Azure アカウントの対応する Text Analytics リソースを使用して、Azure に課金情報を送信します。 Text Analytics コンテナーによって、課金目的で次のコマンド ライン オプションが使用されます。

| オプション | 説明 |
|--------|-------------|
| `ApiKey` | 課金情報を追跡するために使用される Text Analytics リソースの API キー。<br/>このオプションの値には、`Billing` に指定されたプロビジョニング済みの Text Analytics Azure リソースの API キーが設定されている必要があります。 |
| `Billing` | 課金情報を追跡するために使用される Text Analytics リソースのエンドポイント。<br/>このオプションの値には、プロビジョニング済みの Text Analytics Azure リソースのエンドポイント URI が設定されている必要があります。|
| `Eula` | コンテナーのライセンスに同意していることを示します。<br/>このオプションの値は `accept` に設定する必要があります。 |

> [!IMPORTANT]
> 3 つすべてのオプションに有効な値が指定されている必要があります。そうでないと、コンテナーが起動しません。

これらのオプションの詳細については、「[コンテナーの構成](../text-analytics-resource-container-config.md)」を参照してください。

## <a name="summary"></a>まとめ

この記事では、Text Analytics コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて説明しました。 要約すると:

* Text Analytics では、キー フレーズ抽出、言語検出、感情分析をカプセル化する、Docker 用の 3 つの Linux コンテナーが提供されます。
* コンテナー イメージは、Azure の Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、Text Analytics コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次の手順

* 構成設定について、[コンテナーの構成](../text-analytics-resource-container-config.md)を確認する
* [Text Analytics の概要](../overview.md)を確認して、キー フレーズ抽出、言語検出、感情分析について学ぶ。  
* コンテナーでサポートされるメソッドの詳細について、[Text Analytics API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) を参照する。
* [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md) を参照して、Computer Vision 機能に関連する問題を解決する。