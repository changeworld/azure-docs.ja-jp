---
title: データを処理するための関数を Azure に設定する
titleSuffix: Azure Digital Twins
description: デジタル ツインにアクセスしてトリガーできる関数を Azure で作成する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8aaa1b2865b1d0f39e6cb224c3979b4f53eeee81
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110066720"
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

Visual Studio 2019 で、 **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** の順に選択します。 **Azure Functions** テンプレートを検索します。 **[次へ]** を選択します。

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio の新規プロジェクト ダイアログを示すスクリーンショット。Azure Functions のプロジェクト テンプレートが強調表示されています。":::

関数アプリの名前を指定し、 __[作成]__ を選択します。

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="新しいプロジェクトを構成するためのダイアログが表示されている Visual Studio のスクリーンショット。プロジェクト名、保存場所、新しいソリューションを作成するための選択肢、ソリューション名などの設定があります。":::

関数アプリの種類として **[イベント グリッド トリガー]** を選択し、 __[作成]__ を選択します。

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="新しい Azure Functions アプリケーションを作成するためのダイアログが表示されている Visual Studio のスクリーンショット。[イベント グリッド トリガー] オプションが強調表示されています。":::

関数アプリの作成後に、Visual Studio によってプロジェクト フォルダー内の *Function1.cs* ファイルにコード サンプルが生成されます。 この短い関数は、イベントをログに記録するために使用されます。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio のスクリーンショット。新しいプロジェクトのプロジェクト ウィンドウが表示され、サンプル関数のコードが、Function1 という名前のファイルに表示されます。" lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>イベント グリッド トリガーを持つ関数を記述する

関数アプリに SDK を追加して関数を作成することができます。 関数アプリは、[.NET (C#) 用の Azure Digital Twin SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) を使用して Azure Digital Twins と対話します。 

この SDK を使用するには、次のパッケージをプロジェクトに含める必要があります。 Visual Studio NuGet パッケージ マネージャーを使用してパッケージをインストールします。 または、コマンドライン ツールで `dotnet` を使用してパッケージを追加します。

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

次に、Visual Studio ソリューション エクスプローラーで、サンプル コードを含む _Function1.cs_ ファイルを開きます。 パッケージ用の次の `using` ステートメントを追加します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>関数に認証コードを追加する

次に、クラス レベルの変数を宣言し、認証コードを追加します。これで、関数が Azure Digital Twins にアクセスできます。 変数とコードを、_Function1.cs_ ファイル内の関数に追加します。

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

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-the-publication-of-your-function"></a>関数の発行を確認する

1. [Azure portal](https://portal.azure.com/) で、自分の資格情報を使用してサインインします。
2. ウィンドウの上部にある検索ボックスで、作成した関数アプリの名前を検索し、選択します。

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Azure portal を示すスクリーンショット。検索フィールドに関数アプリ名を入力します。" lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. 表示された **[関数アプリ]** ページの左側のメニューで **[関数]** を選択します。 関数が正常に発行された場合は、その名前がリストに表示されます。

    > [!Note] 
    > 発行された関数のリストに関数が表示されるまでに、数分待つか、ページを数回更新する必要がある場合があります。

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Azure portal で発行された関数の表示。" lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Azure Digital Twins にアクセスするために、関数アプリには、Azure Digital Twins インスタンスにアクセスできるアクセス許可を持つシステム マネージド ID が必要です。 次はその設定をします。

## <a name="set-up-security-access-for-the-function-app"></a>関数アプリのセキュリティ アクセスを設定する

Azure CLI または Azure portal を使用して、関数アプリに対するセキュリティ アクセスを設定できます。 好みに合わせて、次の手順に従ってください。

# <a name="cli"></a>[CLI](#tab/cli)

これらのコマンドを、[Azure Cloud Shell](https://shell.azure.com) または[ローカルにインストールされた Azure CLI](/cli/azure/install-azure-cli) で実行します。
関数アプリのシステム マネージド ID を使用して、それに Azure Digital Twins インスタンスの "**Digital Twins データ所有者**" ロールを付与できます。 このロールにより、インスタンスでデータ プレーン アクティビティを実行するアクセス許可が関数アプリに付与されます。 次に、環境変数を設定することで、関数からインスタンスの URL にアクセスできるようにします。

### <a name="assign-an-access-role"></a>アクセス ロールを割り当てる

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

前の例の関数スケルトンには、ベアラー トークンが渡される必要があります。 ベアラー トークンが渡されない場合は、関数アプリが Azure Digital Twins で認証できません。 

ベアラー トークンが必ず渡されるようにするには、関数アプリが Azure Digital Twins にアクセスできるように、 [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) アクセス許可を設定します。 このアクセス許可は、各関数アプリに 1 回だけ設定します。


1. 次のコマンドを使用して、関数のシステム マネージド ID の詳細を確認します。 出力の `principalId` フィールドを書き留めてください。

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-App-Service-(function-app)-name>   
    ```

    >[!NOTE]
    > 結果が空で、ID の詳細が表示されない場合は、次のコマンドを使用して、関数の新しいシステム マネージド ID を作成します。
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-App-Service-(function-app)-name>    
    >```
    >
    > 出力には、次の手順に必要な `principalId` 値を含む、ID の詳細が表示されます。 

1. 次のコマンドで `principalId` 値を使用して、関数アプリの ID を、Azure Digital Twins インスタンスの _Azure Digital Twins データ所有者_ ロールに割り当てます。

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>アプリケーション設定の構成

環境変数を設定することで、関数からインスタンスの URL にアクセスできるようにします。 環境変数の詳細については、「[お使いの関数アプリの管理](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)」を参照してください。 

> [!TIP]
> Azure Digital Twins インスタンスの URL を作成するには、インスタンスのホスト名の先頭に *https://* を追加します。 インスタンスのすべてのプロパティと共にホスト名を表示するには、`az dt show --dt-name <your-Azure-Digital-Twins-instance>` を実行します。

```azurecli-interactive 
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

[Azure portal](https://portal.azure.com/) で次の手順を実行します。

### <a name="assign-an-access-role"></a>アクセス ロールを割り当てる

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

システム割り当てマネージド ID によって、コード内に資格情報を格納せずに、Azure リソースをクラウド サービス (Azure Key Vault など) に対して認証させることができます。 システム割り当てマネージド ID を有効にした後は、Azure のロールベースのアクセス制御によって、必要なすべてのアクセス許可を付与できます。 

この種類のマネージド ID のライフサイクルは、このリソースのライフサイクルに関連付けられています。 また、各リソースが持つことができるシステム割り当てマネージド ID は 1 つのみです。

1. [Azure portal](https://portal.azure.com/) で検索ボックスに名前を入力して関数アプリを検索します。 結果からアプリを選択します。 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure portal のスクリーンショット: 関数アプリ名がポータルの検索バーに入力され、検索結果が強調表示されています。":::

1. 関数アプリのページで、左側のメニューの __[ID]__ を選択して、関数のマネージド ID を操作します。 __[システム割り当て済み]__ ページで、 __[状態]__ が **[オン]** に設定されていることを確認します。 そうでない場合は、ここで設定し、変更を **保存** します。

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Azure portal のスクリーンショット: 関数アプリの [ID] ページで、[状態] オプションが [オン] に設定されています。" lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. __[Azure でのロールの割り当て]__ を選択します。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Azure portal のスクリーンショット。Azure Function の [ID] ページの [アクセス許可] で、[Azure でのロールの割り当て] ボタンが強調表示されています。" lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    __[+ ロールの割り当ての追加 (プレビュー)]__ を選択します。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Azure portal のスクリーンショット。[Azure でのロールの割り当て] ページで [ロールの割り当ての追加 (プレビュー)] ボタンが強調表示されています。" lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. __[ロールの割り当ての追加 (プレビュー)]__ ページで、次の値を選択します。

    * **スコープ**: _リソース グループ_
    * **サブスクリプション**:Azure サブスクリプションを選択します。
    * **リソース グループ**: リソース グループを選択します。
    * **ロール**: _Azure Digital Twins データ所有者_

    __[保存]__ を選択して設定を保存します。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="新しいロールの割り当てを追加する方法を示す Azure portal のスクリーンショット。ダイアログには、スコープ、サブスクリプション、リソース グループ、およびロールの各フィールドが表示されています。":::

### <a name="configure-application-settings"></a>アプリケーション設定の構成

関数から Azure Digital Twins インスタンスの URL にアクセスできるようにするには、そのための環境変数を設定します。 アプリケーションの設定は環境変数として公開され、これを使用して、Azure Digital Twins インスタンスへのアクセスを許可できます。 環境変数の詳細については、「[お使いの関数アプリの管理](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)」を参照してください。 

環境変数にインスタンスの URL を設定するには、まず、インスタンスのホスト名を検索します。 

1. [Azure portal](https://portal.azure.com) でインスタンスを検索します。 
1. 左側のメニューで __[概要]__ を選択します。 
1. __[ホスト名]__ の値をコピーします。

    :::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Azure portal のスクリーンショット。インスタンスの [概要] ページで、ホスト名の値が強調表示されています。":::

これで、アプリケーションの設定を作成できます。

1. ポータルの検索バーで関数アプリを検索し、見つかった関数アプリを選択します。

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure portal のスクリーンショット。関数アプリの名前がポータルの検索バーで検索され、検索結果が強調表示されています。":::

1. 左側のメニューで __[構成]__ を選択します。 次に、 __[アプリケーションの設定]__ タブで、 __[+ 新しいアプリケーション設定]__ を選択します。

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure portal のスクリーンショット。関数アプリの [構成] タブで、新しいアプリケーション設定を作成するためのボタンが強調表示されています。":::

1. 開いたウィンドウで、コピーしたホスト名の値を使用してアプリケーション設定を作成します。
    * **[名前]** : ADT_SERVICE_URL
    * **[値]** : https://{your-azure-digital-twins-host-name}
    
    __[OK]__ を選択して、アプリケーションの設定を作成します。
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure portal のスクリーンショット。[アプリケーション設定の追加/編集] ページで、[名前] および [値] フィールドに値が入力されています。[OK] ボタンが強調表示されています。":::

1. 設定を作成すると、 __[アプリケーションの設定]__ タブに表示されます。**ADT_SERVICE_URL** がリストに表示されていることを確認します。 次に、 __[保存]__ を選択して、新しいアプリケーション設定を保存します。

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure portal のスクリーンショット。[アプリケーションの設定] タブで、新しい [A D T SERVICE U R L] 設定が強調表示され、[保存] ボタンも強調表示されています。":::

1. アプリケーション設定に加えた変更を有効にするには、アプリケーションを再起動する必要があるため、確認のメッセージが表示されたら、 __[続行]__ を選択してアプリケーションを再起動します。

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure portal のスクリーンショット。アプリケーション設定を変更するとアプリケーションが再起動されるというメモが表示されています。[続行] ボタンが強調表示されています。":::

---

## <a name="next-steps"></a>次の手順

この記事では、Azure Digital Twins で使用する関数アプリを Azure で設定しました。 次に、[IoT Hub データを Azure Digital Twins に取り込む](how-to-ingest-iot-hub-data.md)基本的な関数を構築する方法を調べます。
