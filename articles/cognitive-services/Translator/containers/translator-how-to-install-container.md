---
title: Translator API 用の Docker コンテナーのインストールと実行
titleSuffix: Azure Cognitive Services
description: Translator API 用の Docker コンテナーを使用してテキストを翻訳します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 10/14/2021
ms.author: lajanuar
recommendations: false
keywords: オンプレミス, Docker, コンテナー, 識別
ms.openlocfilehash: dbee487212f56eb5c99d3cc785cf8a2205280fcf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426147"
---
# <a name="install-and-run-translator-containers-preview"></a>Translator コンテナーをインストールして実行する (プレビュー)

コンテナーを使用すると、独自の環境で Translator サービスのいくつかの機能を実行できます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。 この記事では、Translator コンテナーをダウンロード、インストール、実行する方法について学習します。

Translator コンテナーを使用すると、堅牢なクラウド機能とエッジの局所性の両方のために最適化された翻訳アプリケーション アーキテクチャを構築できます。

Translator コンテナーの使用時には、[サポートされる言語](../language-support.md)の一覧を参照してください。 

> [!IMPORTANT]
>
> * Translator コンテナーは限定的なプレビューであり、使用するには、オンライン要求を送信して承認を受けている必要があります。 詳細については、「[コンテナーを実行するための承認を要求する](#request-approval-to-run-container)」を参照してください。
> * Translator コンテナーでは、クラウド オファリングと比較して限定された機能がサポートされています。  詳細については、「**コンテナー: Translate**」を参照してください。

<!-- markdownlint-disable MD033 -->

## <a name="prerequisites"></a>前提条件

開始するには、アクティブな [**Azure アカウント**](https://azure.microsoft.com/free/cognitive-services/)が必要です。  アカウントがない場合は、[**無料アカウントを作成**](https://azure.microsoft.com/free/)できます。

以下も必要となります。

| 必須 | 目的 |
|--|--|
| Docker に関する知識 | <ul><li>レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な`docker`[用語とコマンドの知識](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)が必要です。</li></ul> |
| Docker エンジン | <ul><li>[ホスト コンピューター](#host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。</li><li> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 </li><li> **Windows** では、**Linux** コンテナーをサポートするように Docker を構成することも必要です。</li></ul> |
| Translator リソース | <ul><li>'global' 以外のリージョン、関連付けられた API キー、エンドポイント URI を持つ Azure の [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) リソース。 コンテナーを開始するには両方の値が必要であり、リソースの概要ページで確認できます。</li></ul>|
|||

|オプション|目的|
|---------|----------|
|Azure CLI (コマンド ライン インターフェイス) |<ul><li> [Azure CLI](/cli/azure/install-azure-cli) を使用すると、一連のオンライン コマンドを使用して Azure リソースを作成および管理できます。 Windows、macOS、Linux 環境にインストールでき、Docker コンテナーと Azure Cloud Shell で実行できます。</li></ul> |
|||

## <a name="required-elements"></a>必須の要素

すべての Cognitive Services コンテナーには、次の 3 つの主要な要素が必要です。

* **EULA の同意設定**。 `Eula=accept` の値で設定されたエンドユーザー使用許諾契約書 (EULA)。

* **API キー** と **エンドポイント URL**。  この API キーは、コンテナーを開始するために使用されます。 API キーとエンドポイント URL の値を取得するには、Translator リソースの _[キーとエンドポイント]_ ページに移動し、`Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> アイコンを選択します。

> [!IMPORTANT]
>
> * サブスクリプション キーは、Cognitive Service API にアクセスするために使用されます。 キーを共有しないでください。 Azure Key Vault を使用するなどして、安全に保管してください。 これらのキーを定期的に再生成することもお勧めします。 API 呼び出しを行うために必要なキーは 1 つだけです。 最初のキーを再生成するときに、2 番目のキーを使用してサービスに継続的にアクセスすることができます。

## <a name="host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

## <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表では、Translator コンテナーの最小仕様と推奨仕様を説明します。 少なくとも 2 ギガバイト (GB) のメモリが必要であり、各 CPU は 2.6 ギガヘルツ (GHz) 以上である必要があります。 各 Translator に割り当てるメモリ (ギガバイト (GB) 単位)。 次の表に、各 Translator コンテナーに割り当てるリソースの最小値と推奨値を示します。

| コンテナー | 最小値 |推奨 | 言語ペア |
|-----------|---------|---------------|----------------------|
| 接続された Translator |2 コア、2 GB のメモリ |4 コア、8 GB メモリ | 4 |
|||

すべての言語ペアについて、2 GB のメモリを使用することをお勧めします。 既定では、Translator オフライン コンテナーには 4 つの言語ペアがあります。 コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

> [!NOTE]
>
> * CPU コアとメモリは、docker run コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。
>
> * 最小仕様と推奨仕様は、Docker の制限に基づくもので、ホスト マシンのリソースに基づくものではありません。

## <a name="request-approval-to-run-container"></a>コンテナーを実行するための承認を要求する

コンテナーへのアクセスを要求するには、[**限定サービス用 Azure Cognitive Services アプリケーション**](https://aka.ms/csgate-translator)に記入して送信します。

[!INCLUDE [Request access to public preview](../../../../includes/cognitive-services-containers-request-access.md)]

## <a name="get-container-images-with-docker-commands"></a>**docker コマンド** を使用してコンテナー イメージを取得する

> [!IMPORTANT]
>
> * 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。
> * コンテナーを実行するには、`EULA`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。

Microsoft Container Registry からコンテナー イメージをダウンロードして実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。

```Docker
docker run --rm -it -p 5000:80 --memory 12g --cpus 4 \
-v /mnt/d/TranslatorContainer:/usr/local/models \
-e apikey={API_KEY} \
-e eula=accept \
-e billing={ENDPOINT_URI} \
-e Languages=en,fr,es,ar,ru  \
mcr.microsoft.com/azure-cognitive-services/translator/text-translation
```

上のコマンドの内容は次のとおりです。

* コンテナー イメージから Translator コンテナーをダウンロードして実行します。
* 12 ギガバイト (GB) のメモリと 4 つの CPU コアを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* 使用許諾契約書 (EULA) に同意します
* 課金エンドポイントを構成します
* 英語、フランス語、スペイン語、アラビア語、ロシア語の翻訳モデルをダウンロードします
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

### <a name="run-multiple-containers-on-the-same-host"></a>同じホスト上で複数のコンテナーを実行する

公開されているポートを使って複数のコンテナーを実行する予定の場合、必ず各コンテナーを別の公開されているポートで実行してください。 たとえば、最初のコンテナーをポート 5000 上で、2 番目のコンテナーを 5001 上で実行します。

このコンテナーと、別の Azure Cognitive Services コンテナーを HOST 上で同時に実行することができます。 同じ Cognitive Services コンテナーの複数のコンテナーを実行することもできます。

## <a name="query-the-containers-translator-endpoint"></a>コンテナーの Translator エンドポイントに対するクエリを実行する

 コンテナーには、REST ベースの Translator エンドポイント API が用意されています。 要求の例を以下に示します。

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en&to=zh-HANS"
    -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

> [!NOTE]
> コンテナーの準備が整う前に cURL POST 要求を実行しようとすると、「*サービスは一時的に利用できません*」という応答を最終的に受け取ります。 コンテナーの準備が整うまで待ってから、もう一度やり直してください。

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshoot"></a>トラブルシューティング

### <a name="validate-that-a-container-is-running"></a>コンテナーが実行されていることを検証する

コンテナーが実行されていることを検証する方法は複数あります。

* コンテナーでは、コンテナーが実行されていることを視覚的に検証するために、`\` にホームページを用意しています。

* 使い慣れた Web ブラウザーを開いて、問題のコンテナーの外部 IP アドレスと公開ポートに移動できます。 以下に示した各種の要求 URL を使用して、コンテナーが実行中であることを確認します。 以下に示した例の要求 URL は `http://localhost:5000` ですが、実際のコンテナーは異なる可能性があります。 実際のコンテナーの **外部 IP アドレス** と公開ポートに移動していることに注意してください。

| 要求 URL | 目的 |
|--|--|
| `http://localhost:5000/` | コンテナーには、ホーム ページが用意されています。 |
| `http://localhost:5000/ready` | GET で要求します。 これにより、コンテナーがモデルに対するクエリを受け取る準備ができていることを確認できます。  この要求は Kubernetes の [liveness probe と readiness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) に対して使用できます。 |
| `http://localhost:5000/status` | GET で要求します。  これにより、コンテナーを開始するために使用された API キーが有効であるかどうかを、エンドポイント クエリを発生させずに確認できます。 この要求は Kubernetes の [liveness probe と readiness probe](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) に対して使用できます。 |
| `http://localhost:5000/swagger` | コンテナーには、エンドポイントの完全なドキュメント一式と、 **[Try it out]\(試してみる\)** の機能が用意されています。 この機能を使用すると、コードを一切記述することなく、お客様の設定を Web ベースの HTML フォームに入力したりクエリを実行したりできます。 クエリから戻った後、HTTP ヘッダーと HTTP 本文の必要な形式を示すサンプル CURL コマンドが得られます。 |

:::image type="content" source="../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png" alt-text="コンテナーのホーム ページ":::

[!INCLUDE [Diagnostic container](../../containers/includes/diagnostics-container.md)]

## <a name="text-translation-code-samples"></a>テキスト翻訳コード サンプル

### <a name="translate-text-with-swagger"></a>Swagger を使用してテキストを翻訳する

#### <a name="english-leftrightarrow-german"></a>英語 &leftrightarrow; ドイツ語

Swagger のページ (`<http://localhost:5000/swagger/index.html>`) に移動する

1. **[POST /translate]\(POST /翻訳\)** を選択します
1. **[試してみる]** を選択します
1. `en` として、**From** パラメータを入力します
1. `de` として、**To** パラメータを入力します
1. `3.0` として、**api-version** パラメーターを入力します
1. **[テキスト]** の下で、`string` を次の JSON に置き換えます

```json
  [
        {
            "text": "hello, how are you"
        }
  ]
```

**[実行]** を選択すると、結果として得られる翻訳が **[応答本文]** に出力されます。 次の応答に似た内容が表示されます。

```json
"translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de"
      }
    ]
```

### <a name="translate-text-with-python"></a>Python を使用してテキストを翻訳する

```python
import requests, json

url = 'http://localhost:5000/translate?api-version=3.0&from=en&to=fr'
headers = { 'Content-Type': 'application/json' }
body = [{ 'text': 'Hello, how are you' }]

request = requests.post(url, headers=headers, json=body)
response = request.json()

print(json.dumps(
    response,
    sort_keys=True,
     indent=4,
     ensure_ascii=False,
     separators=(',', ': ')))
```

### <a name="translate-text-with-cnet-console-app"></a>C#/.NET コンソール アプリでテキストを翻訳する

Visual Studio を起動し、新しいコンソール アプリケーションを作成します。 `*.csproj` ファイルを編集して、`<LangVersion>7.1</LangVersion>` ノードを追加します。これは、C# 7.1 を指定します。 [Newtoonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet パッケージ、バージョン 11.0.2 を追加します。

`Program.cs` で、すべての既存のコードを次のコードに置き換えます。

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
        const string TranslateApi = "/translate?api-version=3.0&from=en&to=de";

        static async Task Main(string[] args)
        {
            var textToTranslate = "Sunny day in Seattle";
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

## <a name="summary"></a>まとめ

この記事では、Translator コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて説明しました。 次のことが理解できました。

* Translator では、Docker 用の Linux コンテナーを提供します。
* コンテナー イメージは、Docker のコンテナー レジストリからダウンロードされて、実行されます。
* REST API を使用して、コンテナーのホスト URI を指定することによって、Translator コンテナーで 'translate' 操作を呼び出すことができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ Azure Cognitive Services コンテナーの詳細情報](../../containers/index.yml?context=%2fazure%2fcognitive-services%2ftranslator%2fcontext%2fcontext)
