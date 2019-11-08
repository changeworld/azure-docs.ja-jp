---
title: コンテナーをインストールして実行する - Translator Text
titleSuffix: Azure Cognitive Services
description: このチュートリアルでの Translator Text Analytics のコンテナーのダウンロード、インストール、および実行方法。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506822"
---
# <a name="install-and-run-translator-text-containers"></a>Translator Text コンテナーをインストールして実行する

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

コンテナーを使用すると、独自の環境で Translator Text API を実行でき、セキュリティとデータ ガバナンスの固有の要件に対応できます。

## <a name="prerequisites"></a>前提条件

Translator Text コンテナーを使用する前に、次の前提条件を満たす必要があります。

|必須|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。|

## <a name="request-access-to-the-container-registry"></a>コンテナー レジストリへのアクセスの要求

コンテナーへのアクセスを要求するには、最初に [Cognitive Translator Text コンテナー要求フォーム](https://aka.ms/translatorcontainerform)に記入して送信する必要があります。

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>ホスト コンピューター

ホストとは、Docker コンテナーを実行する Linux OS を使用している x64 ベースのコンピューターのことです。 お客様のオンプレミス上のコンピューターを使用できるほか、次のような Azure 内の Docker ホスティング サービスを使用することもできます。

* Azure Kubernetes Service。
* Azure Container Instances。
* Azure Stack にデプロイされた [Kubernetes](https://kubernetes.io/) クラスター。

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、各 Translator Text コンテナーに割り当てる CPU コア (2.6 GHz (ギガヘルツ) 以上) とメモリ (GB 単位) の最小値と推奨値を示します。

| コンテナー | 最小値 | 言語ペア |
|-----------|---------|---------------|
| Translator Text | 4 コア、4 GB メモリ | 4 |

すべての言語ペアについて、1 GB のメモリを使用することをお勧めします。 既定では、Translator Text コンテナーには、実行している `<image-tag>` に応じて、3 つまたは 4 つの言語ペアがあります。 詳細については、「[サポートされる言語と翻訳](#supported-languages-and-translation)」を参照してください。 コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

Translator Text のコンテナー イメージは、次のコンテナー リポジトリで入手できます。 また、この表は、コンテナー イメージ タグとそれに対応するサポートされる言語をマップしています。

| コンテナー | イメージ タグ | サポートされている言語 |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`、`zh-CN`、`de-DE`、および `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`、`fr-FR`、`es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>Translator Text コンテナー用の Docker pull

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを実行するには、まずコンテナー レジストリにアクセスする必要があります。 Azure CLI から、[`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login) コマンドを使用して Azure Container Registry にログインできます。

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

このコマンドは、対応する Azure Container Registry を使用して現在のユーザーを認証します。 これで、`docker pull` コマンドを自由に実行できるようになりました。

> [!NOTE]
> 必要な言語サポートに応じて、対応する `<image-tag>` を指定します。

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. [コンテナーを実行します](#run-the-container-with-docker-run)。 `docker run` コマンドの他の[例](translator-text-container-config.md#example-docker-run-commands)もご覧いただけます。
1. [コンテナーの翻訳エンドポイントに対するクエリを実行します](#query-the-containers-translate-endpoint)。

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

3 つのコンテナーのいずれかを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 `docker run` コマンドの[例](translator-text-container-config.md#example-docker-run-commands)を利用できます。

### <a name="run-container-example-of-docker-run-command"></a>docker run コマンドのコンテナー実行の例

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

このコマンドは、次の操作を行います。

* コンテナー イメージから Translator Text コンテナーを実行します
* 4 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* 使用許諾契約書 (EULA) に同意します
* コンテナーの終了後にそれを自動的に削除します。 コンテナー イメージは引き続きホスト コンピューター上で使用できます

### <a name="how-to-collect-docker-logs"></a>Docker ログを収集する方法

コンテナー実行の Docker ログを表示すると、トラブルシューティングの際に便利な場合があります。 最初に、書式設定フラグを指定して [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) コマンドを実行して、すべてのコンテナーに表示される詳細を制限します。

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

次に、該当するコンテナーの `<Container ID>` と一緒に [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) コマンドを実行して、ログを表示します。

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

上記の docker logs コマンドによって、過去 4 時間の "エラー" ログが収集されます。

## <a name="supported-languages-and-translation"></a>サポートされる言語と翻訳

`POST /translate` メソッドは、*英語*から次のようなターゲット言語、およびその逆の変換をサポートしています。 ここに一覧で示されている言語と英語を相互に切り換えることはできますが、*英語以外*の言語から別の*英語以外*の言語に変換することは*できない*ことに注意してください。

> [!NOTE]
> 最適な品質を得るために、コンシューマーは要求ごとに 1 つの文を送信する必要があります。

| 言語変換 | 言語 ISO 変換 | 画像タグ |
|--|--|:--|
| 英語: left_right_arrow:中国語 | `en-US` :left_right_arrow: `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| 英語: left_right_arrow:ロシア語 | `en-US` :left_right_arrow: `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| 英語: left_right_arrow:アラビア語 | `en-US` :left_right_arrow: `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| 英語: left_right_arrow:ドイツ語 | `en-US` :left_right_arrow: `de-DE` | `ar_de_en_ru_zh_1.0.0` と `de_en_es_fr_1.0.0` |
| 英語: left_right_arrow:スペイン語 | `en-US` :left_right_arrow: `es-ES` | `de_en_es_fr_1.0.0` |
| 英語: left_right_arrow:フランス語 | `en-US` :left_right_arrow: `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> コンテナーを実行するには、`Eula` が指定されている必要があります。そうでない場合、コンテナーが起動しません。

## <a name="query-the-containers-translate-endpoint"></a>コンテナーの翻訳エンドポイントに対するクエリを実行する

コンテナーには、REST ベースの翻訳エンドポイント API が用意されています。 このエンドポイントの使用例をいくつか次に示します。

# <a name="curltabcurl"></a>[cURL](#tab/curl)


目的の CLI から、次の cURL コマンドを実行します。

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> コンテナーの準備が整う前にこの cURL POST を実行しようとすると、"サービスは一時的に利用できません" という応答を最終的に受け取ります。 コンテナーの準備が整うまで待ってから、もう一度やり直してください。

次の出力がコンソールに出力されます。

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

Swagger のページに移動します (http://localhost:5000/swagger/index.html )

1. **[POST /translate]\(POST /翻訳\)** を選択します
1. **[試してみる]** を選択します
1. `en-US` として、**From** パラメータを入力します
1. `de-DE` として、**To** パラメータを入力します
1. `3.0` として、**api-version** パラメーターを入力します
1. **[テキスト]** の下で、`string` を次の JSON に置き換えます
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. **[実行]** を選択すると、結果として得られる翻訳が **[応答本文]** に出力されます。 次のようなテキストが表示されます。
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. Visual Studio を起動し、新しいコンソール アプリケーションを作成します。
1. `*.csproj` ファイルを編集して、`<LangVersion>7.1</LangVersion>` ノードを追加します。これは、C# 7.1 を指定します。
1. [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet パッケージ、バージョン 11.0.2 を追加します。
1. `Program.cs` で、すべての既存のコードを次のコードに置き換えます。
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. **F5** キーを押して、プログラムを実行します。
1. 次の出力がコンソールに出力されます。
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](translator-text-container-config.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>まとめ

この記事では、Translator Text コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて説明しました。 要約すると:

* Translator Text には、さまざまな言語のペアをカプセル化する、Docker 用の複数の Linux コンテナーが用意されています。
* コンテナー イメージは、"コンテナー プレビュー" レジストリからダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、Translator Text コンテナーの操作を呼び出すことができます。

## <a name="next-steps"></a>次の手順

* 構成設定について、[コンテナーの構成](translator-text-container-config.md)を確認する
* [よく寄せられる質問 (FAQ)](../containers/container-faq.md) を参照して、機能に関連する問題を解決します。
