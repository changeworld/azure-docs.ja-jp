---
title: Media Services v3 を使用する Azure Functions の開発
description: この記事では、Visual Studio Code を使用して Media Services v3 で Azure Functions の開発を開始する方法について説明します。
services: media-services
author: xpouyat
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 06/09/2021
ms.author: xpouyat
ms.custom: devx-track-csharp
ms.openlocfilehash: 523a5dbfb503f47f15e44e7be1b61bf6cf05c471
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661718"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>Media Services v3 を使用する Azure Functions の開発

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、Media Services を使用した Azure Functions の作成方法について説明しています。 この記事で定義されている Azure Functions は、ビデオ ファイルを Media Encoder Standard でエンコードします。 エンコード ジョブが作成されるとすぐに、関数はジョブ名と出力アセット名を返します。 Azure Functions について確認するには、**Azure Functions** のセクションで[概要](../../azure-functions/functions-overview.md)およびその他のトピックを参照してください。

Azure Media Services を使用する既存の Azure 関数を探してデプロイするには、[Media Services Azure Functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration) をチェックアウトしてください。 このリポジトリには、Media Services を使ったサンプルが格納されています。Blob Storage からの直接コンテンツ取り込み、エンコード、ライブ ストリーム操作に関連するワークフローの例が紹介されています。

## <a name="prerequisites"></a>前提条件

- 初めての関数を作成するには、アクティブな Azure アカウントを用意しておく必要があります。 Azure アカウントがまだない場合は、 [無料アカウントを利用できます](https://azure.microsoft.com/free/)。
- Azure Media Services (AMS) アカウントでアクションを実行する Azure 関数を作成したり、Media Services から送信されるイベントをリッスンしたりするためには、[こちら](account-create-how-to.md)の説明に従って AMS アカウントを作成する必要があります。
- [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかに [Visual Studio Code](https://code.visualstudio.com/) をインストールします。

この記事では、Azure Media Services と通信する C# .NET 5 関数を作成する方法について説明します。 別の言語で関数を作成するには、こちらの[記事](../../azure-functions/functions-develop-vs-code.md)を参照してください。

### <a name="run-local-requirements"></a>ローカルで実行するための要件

これらの前提条件は、関数をローカルで実行およびデバッグするためにのみ必要です。 プロジェクトの作成、または Azure Functions への発行には必要ありません。

- [.NET Core 3.1 と .NET 5 SDK](https://dotnet.microsoft.com/download/dotnet)。

- [Azure Functions Core Tools](../../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools) バージョン 3.x 以降。 プロジェクトをローカルで開始すると、Core Tools パッケージが自動的にダウンロードされてインストールされます。 Core Tools には、Azure Functions ランタイム全体が含まれているため、ダウンロードとインストールにはしばらく時間がかかる場合があります。

- Visual Studio Code 用の [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

## <a name="install-the-azure-functions-extension"></a>Azure Functions 拡張機能をインストールする

Azure Functions 拡張機能を使用すると、関数を作成してテストし、Azure にデプロイすることができます。

1. Visual Studio Code で **[拡張機能]** を開き、**Azure Functions** を検索するか、Visual Studio Code でこのリンク ([`vscode:extension/ms-azuretools.vscode-azurefunctions`](vscode:extension/ms-azuretools.vscode-azurefunctions)) を選択します。

1. **[インストール]** を選択して、Visual Studio Code に拡張機能をインストールします。

    ![Azure Functions の拡張機能をインストールする](./Media/integrate-azure-functions-dotnet-how-to/vscode-install-extension.png)

1. インストール後、アクティビティ バーの Azure アイコンを選択します。 サイド バーに Azure Functions 領域が表示されます。

    ![サイド バーの Azure Functions 領域](./Media/integrate-azure-functions-dotnet-how-to/azure-functions-window-vscode.png)

## <a name="create-an-azure-functions-project"></a>Azure Functions プロジェクトを作成する

Functions の拡張機能により、最初の関数と共に関数アプリ プロジェクトを作成できます。 次の手順では、HTTP によってトリガーされる関数を新しい Functions プロジェクトに作成する方法を示します。 HTTP トリガーは、実演する最も単純な関数トリガー テンプレートです。

1. **[Azure:Functions]** で **[関数の作成]** アイコンを選択します。

    ![関数を作成する](./Media/integrate-azure-functions-dotnet-how-to/create-function.png)

1. 関数アプリ プロジェクト用のフォルダーを選択し、**関数プロジェクト用 C# を選択** し、ランタイムの **.NET 5 Isolated** を選択します。

1. **HTTP トリガー** 関数テンプレートを選択します。

    ![HTTP トリガー テンプレートを選択する](./Media/integrate-azure-functions-dotnet-how-to/create-function-choose-template.png)

1. 関数名として「**HttpTriggerEncode**」と入力して Enter キーを選択してから、名前空間の **[Company.Function]** を受け入れ、アクセス権限に **[関数]** を選択します。 この承認レベルでは、関数エンドポイントを呼び出すときに[関数キー](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)を指定する必要があります。

    ![関数の承認を選択する](./Media/integrate-azure-functions-dotnet-how-to/create-function-auth.png)

    選択した言語と、HTTP によってトリガーされる関数のテンプレートで、関数が作成されます。

    ![Visual Studio Code の HTTP によってトリガーされる関数のテンプレート](./Media/integrate-azure-functions-dotnet-how-to/new-function-full.png)

## <a name="install-media-services-and-other-extensions"></a>Media Services とその他の拡張機能をインストールする

ターミナル ウィンドウで dotnet add package コマンドを実行して、プロジェクトに必要な拡張機能パッケージをインストールします。 次のコマンドでは、サンプルに必要な Media Services パッケージとその他の拡張機能がインストールされます。

```bash
dotnet add package Azure.Storage.Blobs
dotnet add package Microsoft.Azure.Management.Media
dotnet add package Azure.Identity
```

## <a name="generated-project-files"></a>生成されたプロジェクト ファイル

このプロジェクト テンプレートは、選択した言語でプロジェクトを作成し、必要な依存関係をインストールします。 新しいプロジェクトには次のファイルが含まれます。

* **host.json**:Functions のホストを構成できます。 これらの設定は、関数をローカルで実行している場合と、Azure で実行している場合に適用されます。 詳細については、[host.json](./../../azure-functions/functions-host-json.md) のリファレンスを参照してください。

* **local.settings.json**:関数をローカルで実行するときに使用される設定を保持します。 これらの設定は、関数をローカルで実行するときにのみ使用されます。

    >[!IMPORTANT]
    >local.settings.json ファイルにはシークレットを含めることができるため、それをプロジェクト ソース管理から除外する必要があります。

* 関数を実装する **HttpTriggerEncode.cs** クラス ファイル。

### <a name="httptriggerencodecs"></a>HttpTriggerEncode.cs

これは関数の C# コードです。 その役割は、Media Services の資産またはソース URL を取得し、Media Services でエンコード ジョブを起動することです。 存在しない場合は、作成された変換が使用されます。 作成されると、入力本文に指定されたプリセットが使用されます。 

>[!IMPORTANT]
>HttpTriggerEncode.cs ファイルの完全な内容を[このリポジトリからの `HttpTriggerEncode.cs` ](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/blob/main/Tutorial/HttpTriggerEncode.cs)で置き換えます。

必要な関数を定義したら、 **[保存および実行]** を選択します。

この関数の **Run** メソッドのソース コードは次のとおりです。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-functions-integration/Tutorial/HttpTriggerEncode.cs#Run)]

### <a name="localsettingsjson"></a>local.settings.json

次の内容でファイルを更新し、値を置き換えます。

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "dotnet-isolated",
    "AadClientId": "00000000-0000-0000-0000-000000000000",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "00000000-0000-0000-0000-000000000000",
    "AadTenantId": "00000000-0000-0000-0000-000000000000",
    "AccountName": "amsaccount",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "ResourceGroup": "amsResourceGroup",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000"
  }
}
```

## <a name="test-your-function"></a>関数をテストする

この関数を VS Code でローカルに実行すると、関数は次のように公開されます。 

```url
http://localhost:7071/api/HttpTriggerEncode
```

これをテストするには、Postman を使用して、JSON 入力本文を使用してこの URL に対して POST を実行できます。

JSON 入力本文の例は次のとおりです。

```json
{
    "inputUrl":"https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4",
    "transformName" : "TransformAS",
    "builtInPreset" :"AdaptiveStreaming"
 }
```

この関数は、ジョブと出力のアセット名を含む出力本文を含む 200 OK を返すはずです。

![Postman で関数をテストする](./Media/integrate-azure-functions-dotnet-how-to/postman.png)

## <a name="next-steps"></a>次のステップ

この時点で、Media Services API を呼び出す関数の開発を開始する準備が整いました。

Azure Media Services v3 での Azure Functions の使用に関する詳細と完全なサンプルについては、[Media Services v3 Azure Functions サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/main/Functions)を参照してください。
