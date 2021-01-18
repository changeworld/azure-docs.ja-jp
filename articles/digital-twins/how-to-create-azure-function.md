---
title: データを処理するための関数を Azure で設定する
titleSuffix: Azure Digital Twins
description: デジタル ツインにアクセスしてトリガーできる関数を Azure で作成する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6c4f23406c97d647002fbb3ab4a3544866303cf4
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051345"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>データを処理するために Azure の関数アプリを接続する

データに基づくデジタル ツインの更新は、[Azure Functions](../azure-functions/functions-overview.md) を使用して作成された関数などのコンピューティング リソースを介し、[**イベント ルート**](concepts-route-events.md)を使用して処理されます。 関数を使用すると、以下に対応してデジタル ツインを更新することができます。
* IoT Hub からのデバイス テレメトリ データ
* ツイン グラフ内の別のデジタル ツインから取得されたプロパティ変更または他のデータ

この記事では、Azure Digital Twins で使用する関数を Azure で作成する方法について順を追って説明します。 

説明する手順の概要は次のとおりです。

1. Visual Studio で Azure Functions プロジェクトを作成する
2. [Event Grid](../event-grid/overview.md) トリガーを使用して関数を作成する
3. 認証コードを関数に追加する (Azure Digital Twins にアクセスできるようにするため)
4. 関数アプリを Azure に発行する
5. 関数アプリの[セキュリティ](concepts-security.md) アクセスを設定する

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>前提条件:Azure Digital Twins インスタンスを設定する

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Visual Studio で関数アプリを作成する

Visual Studio 2019 では、 _[ファイル] > [新規作成] > [プロジェクト]_ を選択し、_Azure Functions_ テンプレートを検索して、 _[次へ]_ を選択します。

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: 新しいプロジェクト ダイアログ":::

関数アプリの名前を指定して、 _[作成]_ を選択します。

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: 新しいプロジェクトを構成する":::

関数アプリの種類で *[イベント グリッド トリガー]* を選択して、 _[作成]_ を選択します。

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: Azure Functions のプロジェクトのトリガー ダイアログ":::

関数アプリを作成すると、Visual Studio によって、プロジェクト フォルダーの **function.cs** ファイルにコード サンプルが自動的に入力されます。 この短い関数は、イベントをログに記録するために使用されます。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: サンプル コードが表示されたプロジェクト ウィンドウ":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Event Grid トリガーを使用して関数を作成する

関数アプリに SDK を追加することによって、関数を作成できます。 関数アプリと Azure Digital Twins のやり取りには、[.NET (C#) 用の Azure Digital Twin SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) を使用します。 

SDK を使用するには、次のパッケージをプロジェクトに含める必要があります。 Visual Studio の NuGet パッケージ マネージャーを使用してパッケージをインストールするか、`dotnet` コマンドライン ツールを使用してパッケージを追加することができます。 次のいずれかの方法を選択します。 

**オプション 1: Visual Studio パッケージ マネージャーを使用してパッケージを追加する:**
    
これを行うには、プロジェクトを右クリックし、一覧から _[NuGet パッケージの管理]_ を選択します。 次に、開いたウィンドウで _[参照]_ タブを選択し、次のパッケージを検索します。 _[インストール]_ を選択し、使用許諾契約に "_同意_" してパッケージをインストールします。

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Azure Functions が適切に設定されるように Azure SDK パイプラインを構成するには、以下のパッケージも必要です。 上記と同じ手順を繰り返して、すべてのパッケージをインストールします。

* `System.Net.Http`
* `Azure.Core.Pipeline`

**オプション 2: `dotnet` コマンドライン ツールを使用してパッケージを追加する:**

または、コマンド ライン ツールで次の `dotnet add` コマンドを使用することもできます。
```cmd/sh
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```

次に、Azure Digital Twins を操作するために必要な依存関係をさらに 2 つプロジェクトに追加します。 以下のリンクを使用すると、NuGet のパッケージに移動できます。そこでコンソール コマンド (.NET CLI を含む) を見つけて、それぞれの最新バージョンをプロジェクトに追加できます。
 * [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core)。 これは、[.NET 用 Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) のパッケージです。
 * [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity)。 このライブラリでは、Azure に対する認証を支援するツールが提供されます。

次に、Visual Studio のソリューション エクスプローラーで、サンプル コードが含まれる _function.cs_ ファイルを開きます。その関数に _using_ ステートメントを追加します。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>関数に認証コードを追加する

次に、クラス レベルの変数を宣言し、関数から Azure Digital Twins にアクセスできるようにする認証コードを追加します。 次のものを {関数名}.cs ファイルの関数に追加します。

* 環境変数として ADT サービスの URL を読み取ります。 関数内にハードコーディングするのではなく、環境変数からサービス URL を読み取ることをお勧めします。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* HttpClient インスタンスを保持する静的変数。 HttpClient は作成に比較的コストがかかるため、すべての関数呼び出しでこれを実行する必要がないようにします。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Azure Functions では、マネージド ID の資格情報を使用できます。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* 関数の内部にローカル変数 _DigitalTwinsClient_ を追加して、Azure Digital Twins クライアント インスタンスを関数プロジェクトに保持します。 クラス内でこの変数を static に "*しないでください*"。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* _adtInstanceUrl_ の null チェックを追加し、関数のロジックを try catch ブロックでラップして、例外をキャッチします。

これらの変更を行った後、関数コードは次のようになります。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

## <a name="publish-the-function-app-to-azure"></a>関数アプリを Azure に発行する

プロジェクトを Azure の関数アプリに発行するには、ソリューション エクスプローラーで (ソリューションではなく) 関数プロジェクトを右クリックし、 **[発行]** を選択します。

> [!IMPORTANT] 
> Azure の関数アプリに発行すると、Azure Digital Twins とは関係なく、サブスクリプションに追加料金が発生します。

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: Azure に関数を発行する":::

発行先として **[Azure]** を選択し、 **[次へ]** を選択します。

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: Azure Functions 発行ダイアログで、Azure を選択する":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: 関数発行ダイアログで、コンピューターに基づいて Azure Function App (Windows) または (Linux) を選択する":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: 関数発行ダイアログで、新しい Azure 関数を作成する":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: 関数発行ダイアログで、フィールドに入力し、[作成] を選択する":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: 関数発行ダイアログで、一覧から関数アプリを選択し、完了する":::

次のページで、新しい関数アプリの適切な名前、リソース グループ、その他の詳細を入力します。
関数アプリから Azure Digital Twins にアクセスできるようにするには、システムマネージド ID を割り当て、Azure Digital Twins インスタンスにアクセスできるアクセス許可を付与する必要があります。

次に、CLI または Azure portal を使用して、関数のセキュリティ アクセスを設定できます。 次のいずれかの方法を選択します。

## <a name="set-up-security-access-for-the-function-app"></a>関数アプリのセキュリティ アクセスを設定する
次のいずれかのオプションを使用して、関数アプリに対するセキュリティ アクセスを設定できます。

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>オプション 1: CLI を使用して関数アプリのセキュリティ アクセスを設定する

前の例の関数スケルトンでは、Azure Digital Twins で認証できるようにするために、ベアラー トークンを渡す必要があります。 このベアラー トークンが確実に渡されるようにするには、関数アプリの [マネージド サービス ID (MSI)](../active-directory/managed-identities-azure-resources/overview.md) を設定する必要があります。 これは、関数アプリごとに 1 回だけ実行する必要があります。

システム マネージド ID を作成し、関数アプリの ID を Azure Digital Twins インスタンスの _**Azure Digital Twins Data Owner (Azure Digital Twins データ所有者)**_ ロールに割り当てることができます。 これにより、インスタンスでデータ プレーン アクティビティを実行するアクセス許可が、関数アプリに与えられます。 次に、環境変数を設定することで、関数から Azure Digital Twins インスタンスの URL にアクセスできるようにします。

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

コマンドを実行するには、[Azure Cloud Shell](https://shell.azure.com) を使用します。

次のコマンドを使用して、システムによって管理される ID を作成します。 出力の _principalId_ フィールドを書き留めてください。

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
_principalId_ 値を次のコマンドで使用して、関数アプリの ID を Azure Digital Twins インスタンスの _Azure Digital Twins Data Owner (Azure Digital Twins データ所有者)_ ロールに割り当てます。

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
最後に、環境変数を設定することで、関数から Azure Digital Twins インスタンスの URL にアクセスできるようにします。 環境変数の設定の詳細については、「[*環境変数*](/sandbox/functions-recipes/environment-variables)」を参照してください。 

> [!TIP]
> Azure Digital Twins インスタンスの URL を作成するには、Azure Digital Twins インスタンスの *hostName* の先頭に *https://* を追加します。 インスタンスのすべてのプロパティと共に hostName を表示する場合は、`az dt show --dt-name <your-Azure-Digital-Twins-instance>` を実行できます。

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>オプション 2:Azure portal を使用して関数アプリのセキュリティ アクセスを設定する

システム割り当てマネージド ID によって、コード内に資格情報を格納せずに、Azure リソースをクラウド サービス (Azure Key Vault など) に対して認証させることができます。 有効にすると、Azure ロールベースのアクセス制御を介して必要なすべてのアクセス許可を付与できます。 この種類のマネージド ID のライフサイクルは、このリソースのライフサイクルに関連付けられています。 また、各リソース (仮想マシンなど) は、システム割り当てマネージド ID を 1 つしか持つことができません。

[Azure portal](https://portal.azure.com/) で、前に作成した関数アプリの名前を使用して、検索バーで "_関数アプリ_" を検索します。 一覧から "*関数アプリ*" を選択します。 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Azure portal: 関数アプリを検索する":::

関数アプリ ウィンドウの左側のナビゲーション バーで _[ID]_ を選択して、マネージド ID を有効にします。
_[システム割り当て済み]_ タブで、 _[状態]_ を [オン] に切り替えて、"_保存_" します。 _[システム割り当てマネージド ID を有効化する]_ というポップアップが表示されます。
_[はい]_ ボタンを選択します。 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure portal: システム マネージド ID を有効にする":::

関数が Azure Active Directory に正常に登録されたことを通知で確認できます。

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure portal: 通知":::

また、次のセクションで使用するので、 _[ID]_ ページに表示される **オブジェクト ID** を記録しておきます。

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="後で使用するオブジェクト ID をコピーする":::

### <a name="assign-access-roles-using-azure-portal"></a>Azure portal を使用してアクセス ロールを割り当てる

_[Azure ロールの割り当て]_ ボタンを選択し、 *[Azure ロールの割り当て]* ページを開きます。 次に、 _[+ ロールの割り当ての追加 (プレビュー)]_ を選択します。

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure portal: ロールの割り当てを追加する":::

_[ロールの割り当ての追加 (プレビュー)]_ ページが開かれたら、以下を選択します。

* _スコープ_: リソース グループ
* _[サブスクリプション]_ : Azure サブスクリプションを選択します
* _[リソース グループ]_ : ドロップダウンからリソース グループを選択します
* _[ロール]_ : ドロップダウンから _[Azure Digital Twins Data Owner]\(Azure Digital Twins データ所有者\)_ を選択します

次に、 _[保存]_ ボタンを押して詳細を保存します。

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure portal: ロールの割り当ての追加 (プレビュー)":::

### <a name="configure-application-settings-using-azure-portal"></a>Azure portal を使用してアプリケーションの設定を構成する

環境変数を設定することで、関数から Azure Digital Twins インスタンスの URL にアクセスできるようにします。 詳細については、「[*環境変数*](/sandbox/functions-recipes/environment-variables)」を参照してください。 Digital Twins インスタンスにアクセスするためのアプリケーション設定は、環境変数として公開されます。 

アプリケーションの設定を作成するには、ADT_INSTANCE_URL が必要です。

ADT_INSTANCE_URL は、インスタンス ホスト名に **_https://_** を追加することによって取得できます。 Azure portal の検索バーでインスタンスを検索することにより、Digital Twins インスタンス ホスト名を見つけることができます。 次に、左側のナビゲーション バーの _[概要]_ を選択して、 _[ホスト名]_ を表示します。 この値をコピーして、アプリケーションの設定を作成します。

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure portal: [概要] -> ホスト名をコピーして _[値]_ フィールドで使用する。":::

これで、次の手順に従って、アプリケーションの設定を作成できるようになりました。

* 検索バーで関数アプリの名前を使用してアプリを検索し、一覧から関数アプリを選択します
* 左側のナビゲーション バーで _[構成]_ を選択して、新しいアプリケーション設定を作成します
* _[アプリケーションの設定]_ タブで、 _[+ 新しいアプリケーション設定]_ を選択します

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Azure portal: 既存の関数アプリを検索する":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure portal: アプリケーションの設定を構成する":::

開いたウィンドウで、上でコピーした値を使用して、アプリケーションの設定を作成します。 \
_[名前]_ : ADT_SERVICE_URL \
_[値]_ : https://{Azure Digital Twins ホスト名}

_[OK]_ を選択して、アプリケーションの設定を作成します。

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure portal: アプリケーションの設定を追加する。":::

_[名前]_ フィールドでアプリケーション名を使用して、アプリケーションの設定を確認できます。 その後、 _[保存]_ ボタンを選択して、アプリケーションの設定を保存します。

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure portal: 作成したアプリケーションを表示し、アプリケーションを再起動する":::

アプリケーションの設定を変更したら、アプリケーションを再起動する必要があります。 _[続行]_ を選択して、アプリケーションを再起動します。

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure portal: アプリケーションの設定を保存する":::

_[通知]_ アイコンを選択することで、アプリケーションの設定が更新されたことを確認できます。 アプリケーションの設定が作成されていない場合は、上記のプロセスに従って、アプリケーションの設定の追加を再度試みることができます。

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure portal: アプリケーションの設定の更新に関する通知":::

## <a name="next-steps"></a>次のステップ

この記事では、Azure Digital Twins で使用する関数アプリを Azure 設定する手順を実行しました。 次に、関数を Event Grid にサブスクライブして、エンドポイントでリッスンできるようにします。 このエンドポイントは次のようになります。
* Azure Digital Twins 自体から送信されるメッセージ (プロパティ変更メッセージ、ツイン グラフの[デジタル ツイン](concepts-twins-graph.md)によって生成されるテレメトリ メッセージ、ライフサイクル メッセージなど) を処理するために、Azure Digital Twins にアタッチされた Event Grid エンドポイント
* テレメトリやその他のデバイス イベントを送信するために IoT Hub が使用する IoT システム トピック
* 他のサービスからメッセージを受信する Event Grid エンドポイント

次に、IoT Hub データを Azure Digital Twins に取り込む基本的な関数を構築する方法を確認します。
* [*方法: IoT Hub からテレメトリを取り込む*](how-to-ingest-iot-hub-data.md)
