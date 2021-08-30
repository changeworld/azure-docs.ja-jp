---
title: データを処理するための関数を Azure に設定する
titleSuffix: Azure Digital Twins
description: デジタル ツインにアクセスしてトリガーできる関数を Azure で作成する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 7/14/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: eaa26b4682dc7e984c0c84575d9e87b5255f6c99
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114386975"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>データを処理するために Azure Functions アプリを接続する

コンピューティング リソースを介した[イベント ルート](concepts-route-events.md)を使用して、データに基づいてデジタル ツインを更新できます。 たとえば、[Azure Functions](../azure-functions/functions-overview.md) を使用して作成された関数で、以下に応えてデジタル ツインを更新できます。
* Azure IoT Hub からのデバイス テレメトリ データ。
* プロパティの変更、またはツイン グラフ内の別のデジタル ツインからの他のデータ。

この記事では、Azure Digital Twins で使用する関数を Azure で作成する方法を説明します。 関数を作成するには、次の基本的な手順に従います。

1. Visual Studio で Azure Functions プロジェクトを作成します。
2. [Azure Event Grid](../event-grid/overview.md) トリガーを持つ関数を記述します。
3. 認証コードを関数に追加して、Azure Digital Twins にアクセスできるようにします。
4. 関数アプリを Azure に発行します。
5. 関数アプリに[セキュリティ](concepts-security.md)を設定します。

## <a name="prerequisite-set-up-azure-digital-twins"></a>前提条件: Azure Digital Twins を設定する

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Visual Studio で関数アプリを作成する

Visual Studio を使用して関数アプリを作成する方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#publish-to-azure)」を参照してください。

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>イベント グリッド トリガーを持つ関数を記述する

関数アプリに SDK を追加して関数を作成することができます。 関数アプリは、[.NET (C#) 用の Azure Digital Twin SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) を使用して Azure Digital Twins と対話します。 

この SDK を使用するには、次のパッケージをプロジェクトに含める必要があります。 Visual Studio NuGet パッケージ マネージャーを使用してパッケージをインストールします。 または、コマンドライン ツールで `dotnet` を使用してパッケージを追加します。

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

次に、Visual Studio ソリューション エクスプローラーで、サンプル コードを含む _.cs_ ファイルを開きます。 パッケージ用の次の `using` ステートメントを追加します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>関数に認証コードを追加する

次に、クラス レベルの変数を宣言し、認証コードを追加します。これで、関数が Azure Digital Twins にアクセスできます。 変数とコードを関数に追加します。

* Azure Digital Twins サービスの URL を **環境変数** として読み取るコード。 関数内にハードコーディングするのではなく、環境変数からサービス URL を読み取ることをお勧めします。 この環境変数の値は、[この記事の後半で](#set-up-security-access-for-the-function-app)設定します。 環境変数の詳細については、「[お使いの関数アプリの管理](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)」を参照してください。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **HttpClient インスタンスを保持する静的変数。** HttpClient は、作成コストが割高であるため、すべての関数呼び出しで、これを作成しないようにします。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **マネージド ID 資格情報。**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **ローカル変数 _DigitalTwinsClient_。** 関数の内部にこの変数を追加して Azure Digital Twins クライアント インスタンスを保持します。 クラス内でこの変数を static に "*しないでください*"。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **_adtInstanceUrl_ の null チェック。** adtInstanceUrl の null チェックを追加し、関数のロジックを、try/catch ブロックで囲んで例外をすべてキャッチします。

これらの変更を行った後は、関数コードが次の例のようになります。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

これで、アプリケーションが作成されたので、これを Azure に発行できます。

## <a name="publish-the-function-app-to-azure"></a>関数アプリを Azure に発行する

関数アプリを発行する方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#publish-to-azure)」を参照してください。

### <a name="verify-the-publication-of-your-function"></a>関数の発行を確認する

1. [Azure portal](https://portal.azure.com/) で、自分の資格情報を使用してサインインします。
2. ウィンドウの上部にある検索ボックスで、作成した関数アプリの名前を検索し、選択します。

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Azure portal を示すスクリーンショット。検索フィールドに関数アプリ名を入力します。" lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. 表示された **[関数アプリ]** ページの左側のメニューで **[関数]** を選択します。 関数が正常に発行された場合は、その名前がリストに表示されます。

    > [!Note] 
    > 発行された関数のリストに関数が表示されるまでに、数分待つか、ページを数回更新する必要がある場合があります。

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Azure portal で発行された関数が表示されているスクリーンショット。" lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Azure Digital Twins にアクセスするために、関数アプリには、Azure Digital Twins インスタンスにアクセスできるアクセス許可を持つシステム マネージド ID が必要です。 次はその設定をします。

## <a name="set-up-security-access-for-the-function-app"></a>関数アプリのセキュリティ アクセスを設定する

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="next-steps"></a>次の手順

この記事では、Azure Digital Twins で使用する関数アプリを Azure で設定しました。 次に、[IoT Hub データを Azure Digital Twins に取り込む](how-to-ingest-iot-hub-data.md)基本的な関数を構築する方法を調べます。
