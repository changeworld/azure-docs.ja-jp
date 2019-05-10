---
title: コンテナーのインストールと実行 - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer のコンテナーのダウンロード、インストール、および実行方法。
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 2a62fd288f9118882e5cd9899ab572d4b247fc5a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156800"
---
# <a name="install-and-run-personalizer-containers"></a>Personalizer コンテナーのインストールと実行

Personalizer サービスには、次のコンテナーがあります。 

|Function|機能|
|-|-|
|personalizer|コンテンツとユーザーの現在のコンテキストから最適なアクションを決定します。|

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

Personalizer サービス コンテナーを使用する前に、次の前提条件を満たす必要があります。

|必須|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。| 
|Personalizer サービスのリソース |これらのコンテナーを使用するためには、以下が必要です。<br><br>関連付けられている課金キーと課金エンドポイント URI を取得するための "_Personalizer サービス_" Azure リソース。 どちらの値も、Azure portal の Personalizer サービスの [概要] ページと [キー] ページで入手でき、コンテナーを起動するために必要です。<br><br>**{BILLING_KEY}**: リソース キー<br><br>**{BILLING_ENDPOINT_URI}**: エンドポイントURI の例: `https://westus.api.cognitive.microsoft.com/`|

### <a name="the-host-computer"></a>ホスト コンピューター

**ホスト**とは、Docker コンテナーを実行するコンピューターのことです。 お客様のオンプレミス上のコンピューターを使用できるほか、次のような Azure 内の Docker ホスティング サービスを使用することもできます。

* [Azure Kubernetes Service](https://docs.microsoft.com/aks/index.yml)
* [Azure Container Instances](https://docs.microsoft.com/container-instances/index.yml)
* [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml) にデプロイされた [Kubernetes](https://kubernetes.io/) クラスター。 詳しくは、「[Kubernetes を Azure Stack にデプロイする](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md)」をご覧ください。

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各 Personalizer サービス コンテナーに割り当てる CPU コアとメモリの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
|personalizer | 1 コア、4 GB メモリ | 2 コア、8 GB メモリ |

各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

Rank と Reward の呼び出しに含まれる情報を Azure 内の Personalizer サーバーにリレーするために、コンテナーは Azure EventHub に接続できる必要があります。また、必要な構成と最新の機械学習モデルを読み込むために、Personalizer API に接続できる必要があります。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

Personalizer サービス用のコンテナー イメージを入手できます。 

| コンテナー | リポジトリ |
|-----------|------------|
| personalizer | `mcr.microsoft.com/azure-cognitive-services/personalizer:latest` |

> [!TIP]
> [docker images](https://docs.docker.com/engine/reference/commandline/images/) コマンドを使用して、ダウンロードしたコンテナー イメージを一覧表示できます。 たとえば、次のコマンドは、ダウンロードした各コンテナー イメージの ID、リポジトリ、およびタグが表として書式設定されて表示されます。
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/personalizer                 latest
>  ``` 

### <a name="docker-pull-for-the-personalizer-service-container"></a>Personalizer サービス コンテナー用の docker pull

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/personalizer:latest
```

## <a name="how-the-container-works"></a>コンテナーのしくみ

Personalizer コンテナーは Personalizer サービスのクライアント部分を表します。 コンテナーは、実行されると、Azure クラウド内の Personalizer サービスからモデルと構成設定を取得します。 コンテナー サービスでは、この情報を使用して、**rank** と **reward** に対する要求に応答します。 また、コンテナーは Azure クラウド内の Personalizer リソースにこれらの要求を送信します。 その後、この情報は、モデルの更新頻度の現在の設定に基づいて、ご自分の Personalizer ループ モデルのトレーニングに使用されます。 更新されたモデルはコンテナーに送り返されます。 

![Personalizer コンテナーのクライアントのオンプレミス ローカル アーキテクチャ](./media/personalizer-container-howto/personalizer-container-architecture.png)

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](personalizer-container-configuration.md#example-docker-run-commands)もご覧いただけます。 
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

3 つのコンテナーのいずれかを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 このコマンドには、次のパラメーターが使用されます。

| プレースホルダー | 値 |
|-------------|-------|
|{BILLING_KEY} | このキーは、コンテナーを起動するために使用され、Azure portal の Personalizer サービスの [キー] ページで入手できます。  |
|{BILLING_ENDPOINT_URI} | 課金エンドポイント URI の値は、Azure portal の Personalizer サービスの [概要] ページで入手できます。|

次の例の `docker run` コマンドでは、これらのパラメーターをお客様独自の値に置き換えてください。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/personalizer\
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから Personalizer サービス コンテナーを実行します
* 1 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。 

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

### <a name="run-multiple-containers-on-the-same-host"></a>同じホスト上で複数のコンテナーを実行する

公開されているポートを使って複数のコンテナーを実行する予定の場合、必ず各コンテナーを別のポートで実行してください。 たとえば、最初のコンテナーをポート 5000 上で、2 番目のコンテナーを 5001 上で実行します。

`<container-registry>` および `<container-name>` を、使用しているコンテナーの値に置き換えます。 これらを同一のコンテナーにする必要はありません。 Personalizer コンテナーと LUIS コンテナーをホスト上で一緒に実行したり、複数の Personalizer コンテナーを実行したりできます。 

ポート 5000 上で最初のコンテナーを実行します。 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

ポート 5001 上で 2 番目のコンテナーを実行します。


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

後続の各コンテナーは、別のポート上になっている必要があります。 

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。 

コンテナーの API のホストとしては https://localhost:5000 を使用します。

## <a name="stop-the-container"></a>コンテナーの停止

コンテナーをシャットダウンするには、コンテナーが実行されているコマンドライン環境で、**Ctrl + C** キーを押します。

## <a name="troubleshoot"></a>トラブルシューティング

出力[マウント](personalizer-container-configuration.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。 

## <a name="container-api-documentation"></a>コンテナーの API ドキュメント

コンテナーには、エンドポイントの完全なドキュメント一式のほか、`Try it now` 機能が用意されています。 この機能を使用すると、コードを一切記述することなく、お客様の設定を Web ベースの HTML フォームに入力したりクエリを実行したりすることができます。 クエリから戻ると、HTTP ヘッダーと HTTP 本文の必要な形式を示すサンプル CURL コマンドが得られます。 

> [!TIP]
> [OpenAPI 仕様](https://swagger.io/docs/specification/about/)は、`/swagger` という相対 URI からご覧いただけます。コンテナーでサポートされる API 操作が説明されています。 例: 
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>課金

Personalizer サービス コンテナーは、Azure アカウントの "_Personalizer サービス_" リソースを使用して、Azure に課金情報を送信します。 

Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services のコンテナーから Microsoft に顧客データが送信されることはありません。 

`docker run` コマンドでは、次の引数が課金の目的に使用されます。

| オプション | 説明 |
|--------|-------------|
| `ApiKey` | 課金情報を追跡するために使用される "_Personalizer サービス_" リソースの API キー。 |
| `Billing` | 課金情報を追跡するために使用される "_Personalizer サービス_" リソースのエンドポイント。|
| `Eula` | コンテナーのライセンスに同意していることを示します。<br/>このオプションの値は `accept` に設定する必要があります。 |

> [!IMPORTANT]
> 3 つすべてのオプションに有効な値が指定されている必要があります。そうでないと、コンテナーが起動しません。

これらのオプションの詳細については、「[コンテナーの構成](personalizer-container-configuration.md)」を参照してください。

## <a name="summary"></a>まとめ

この記事では、Personalizer サービス コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて説明しました。 要約すると:

* Personalizer サービスは、パーソナル化をカプセル化する、Docker 用の Linux コンテナーを提供します。
* コンテナー イメージは、Azure の Microsoft Container Registry (MCR) からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、Personalizer サービス コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 また、Personalizer コンテナーはオンライン トレーニングのために Azure の対応するサービスに要求データを送信し、更新されたモデルを Azure から定期的に取得します。

## <a name="next-steps"></a>次の手順

[`Docker Run` コマンド用の Docker のコンテナーを構成する方法](personalizer-container-configuration.md)