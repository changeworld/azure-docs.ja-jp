---
title: Azure Functions と API Management を使用して Visual Studio でサーバーレス API を作成する
description: Visual Studio を使用して、HTTP によってトリガーされる関数を OpenAPI 定義と共に作成します。これにより、他のアプリやサービスがサーバーレス関数ベースの API を呼び出すことができる Azure API Management 統合が可能になります。
ms.topic: tutorial
ms.date: 05/07/2021
ms.openlocfilehash: b8c6afc64f15df3124ab4052568bf36d209d4fde
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383983"
---
# <a name="create-serverless-apis-in-visual-studio-using-azure-functions-and-api-management-integration-preview"></a>Azure Functions と API Management 統合 (プレビュー) を使用して Visual Studio でサーバーレス API を作成する

REST API は、多くの場合、OpenAPI 定義を使用して記述されます。 このファイルには、API での操作と、API の要求と応答のデータを構造化する方法に関する情報が含まれます。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * Visual Studio でサーバーレス関数プロジェクトを作成する
> * 組み込みの OpenAPI 機能を使用して関数 API をローカルでテストする
> * API Management 統合を使用して、プロジェクトを Azure の関数アプリに発行する 
> * 関数のアクセス キーを取得し、API Management で設定する
> * OpenAPI 定義ファイルをダウンロードする

作成するサーバーレス関数では、風力タービンの緊急修理のコスト効率が高いかどうかを判断できる API が提供されます。 作成する関数アプリと API Management インスタンスの両方で従量課金プランを使用するため、このチュートリアルを完了するためのコストは最小限です。

> [!NOTE]
> この記事で取り上げる OpenAPI と API Management 統合は、現在プレビュー段階です。 サーバーレス API を公開するこの方法は、C# クラス ライブラリ (.NET Core 3.1) 関数でのみサポートされます。 その他のすべての言語ランタイムでは、代わりに、[ポータルから Azure API Management 統合を使用する必要があります](functions-openapi-definition.md)。 

## <a name="prerequisites"></a>前提条件

+ [Visual Studio 2019](https://azure.microsoft.com/downloads/) バージョン 16.10、または以降のバージョン。 インストール時に **[Azure の開発]** ワークロードを選択する必要があります。 

+ アクティブな [Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)。開始する前に[無料アカウント](https://azure.microsoft.com/free/dotnet/)を作成してください。

## <a name="create-a-functions-project"></a>Functions プロジェクトを作成する

Visual Studio の Azure Functions プロジェクト テンプレートでは、Azure の関数アプリに発行できるプロジェクトを作成します。 また、OpenAPI 定義ファイル (以前の Swagger ファイル) の生成をサポートする HTTP によってトリガーされる関数を作成します。

1. Visual Studio メニューで、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** を選択します。

1. **[新しいプロジェクトの作成]** の検索ボックスに「*functions*」と入力し、**Azure Functions** テンプレートを選択してから、 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクトの **プロジェクト名** (`TurbineRepair` など) を入力し、 **[作成]** を選択します。 

1. **[新しい Azure Functions アプリケーションの作成]** 設定で、次の表の値を使用します。

    | 設定      | 値  | 説明                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET バージョン** | **.NET Core 3 (LTS)** | この値により、Azure Functions のバージョン 3.x ランタイムを使用する関数プロジェクトが作成されます。 OpenAPI ファイルの生成は、Functions ランタイムのバージョン 3.x でのみサポートされます。 |
    | **関数テンプレート** | **OpenAPI を使用した HTTP トリガー** | この値により、OpenAPI 定義ファイルを生成する機能を備えた、HTTP 要求によってトリガーされる関数が作成されます。  |
    | **ストレージ アカウント (AzureWebJobsStorage)**  | **ストレージ エミュレーター** | HTTP トリガー関数のローカル開発のためにエミュレーターを使用できます。 Azure の関数アプリにはストレージ アカウントが必要であるため、プロジェクトを Azure に発行するときに割り当てられるか、作成されます。 |
    | **承認レベル** | **関数** | Azure で実行するクライアントでは、エンドポイントにアクセスするときにキーを指定する必要があります。 キーと認可の詳細については、「[関数のアクセス キー](functions-bindings-http-webhook-trigger.md#authorization-keys)」を参照してください。 |
    
    ![Azure Functions プロジェクトの設定](./media/openapi-apim-integrate-vs/functions-project-settings.png)

1. **[作成]** を選択して、OpenAPI のサポートを使用して、関数プロジェクトと HTTP トリガー関数を作成します。 

Visual Studio によって、プロジェクトと `Function1` という名前のクラスが作成されます。このクラスには、HTTP トリガー関数型のスケルトン コードが含まれます。 次に、この関数テンプレート コードを独自にカスタマイズしたコードに置き換えます。 

## <a name="update-the-function-code"></a>関数コードを更新する

この関数では、2 つのパラメーターを受け取る HTTP トリガーが使用されます。

| パラメーター名 | 説明|
| ---- | ---- |
| *hours* | タービン修復の推定所要時間 (最も近い時間単位に繰り上げ)。 |
| *容量* | タービンの容量 (キロワット単位)。 |

この関数では、修復コストと、タービンによってもたらされる 24 時間あたり収益が計算されます。 パラメーターは、クエリ文字列または POST 要求のペイロードのいずれかで指定されます。 

Function1.cs プロジェクト ファイルで、生成されたクラス ライブラリ コードの内容を次のコードに置き換えます。

:::code language="csharp" source="~/functions-openapi-turbine-repair/TurbineRepair/Function1.cs":::

この関数コードは、応急修復のコスト効果が高いかどうかを示す "`Yes`" または "`No`" のメッセージを返します。 また、タービンによって表される収益機会とタービンの修復コストも返されます。

## <a name="run-and-verify-the-api-locally"></a>API をローカルで実行して検証する

関数を実行すると、OpenAPI エンドポイントにより、生成されたページを使用して関数をローカルで簡単に試すことができます。 ローカルで実行する場合、関数アクセス キーを指定する必要はありません。

1. F5 キーを押して、プロジェクトを開始します。 Functions ランタイムをローカルで開始すると、一連の OpenAPI と Swagger のエンドポイントが関数エンドポイントと共に出力に表示されます。  

1. ブラウザーで、RenderSwaggerUI エンドポイントを開きます。これは、`http://localhost:7071/api/swagger/ui` のようになります。 OpenAPI 定義に基づいて、ページが表示されます。

1. **[POST]**  >  **[Try it out]\(試してみる\)** を選択し、クエリ パラメーターとして、または JSON 要求本文に `hours` および `capacity` の値を入力し、 **[実行]** を選択します。 

    :::image type="content" source="media/openapi-apim-integrate-vs/swagger-ui-post.png" alt-text="TurbineRepair API をテストするための Swagger UI":::

1. `hours` に 6、`capacity` に 2500 のような整数値を入力すると、次の例のような JSON 応答が返されます。
   
    :::image type="content" source="media/openapi-apim-integrate-vs/swagger-ui-response.png" alt-text="TurbineRepair 関数からの応答 JSON データ。":::

これで、応急修復のコスト効率性を確認する関数が作成されました。 次に、プロジェクトと API の定義を Azure に発行します。

## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

プロジェクトを発行するには、Azure サブスクリプションに関数アプリが存在する必要があります。 初めてプロジェクトを発行するときに、Visual Studio の発行機能によって、関数アプリが作成されます。 また、関数アプリと統合することで TurbineRepair API を公開する API Management インスタンスを作成することもできます。

1. **ソリューション エクスプローラー** でプロジェクトを右クリックし、 **[発行]** を選択します。 **[ターゲット]** で **[Azure]** を選択し、 **[次へ]** を選択します。

1. **[特定のターゲット]** で、 **[Azure Function App (Windows)]** を選択して、Windows で動作する関数アプリを作成し、 **[次へ]** を選択します。

1. **[Function Instance]\(関数インスタンス\)** で、 **[+ 新しい Azure 関数を作成...]** を選択します。

    :::image type="content" source="media/openapi-apim-integrate-vs/publish-new-resource.png" alt-text="新しい関数アプリ インスタンスの作成":::

1. 次の表に示されている値を使用して、新しいインスタンスを作成します。

    | 設定      | 値  | 説明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | グローバルに一意の名前 | 新しい関数アプリを一意に識別する名前。 この名前をそのまま使用するか、新しい名前を入力します。 有効な文字は、`a-z`、`0-9`、`-` です。 |
    | **サブスクリプション** | 該当するサブスクリプション | 使用する Azure サブスクリプション。 このサブスクリプションを承諾するか、ドロップダウン リストから新しいものを選択します。 |
    | **[リソース グループ](../azure-resource-manager/management/overview.md)** | リソース グループの名前 |  関数アプリを作成するリソース グループ。 ドロップダウン リストから既存のリソース グループを選択するか、または **[新規]** を選択して新しいリソース グループを作成します。|
    | **[プランの種類](functions-scale.md)** | 従量課金 | [従量課金プラン](consumption-plan.md)で実行される関数アプリにプロジェクトを発行する場合は、関数アプリの実行に対してのみお支払いください。 他のホスティング プランでは、コストが高くなります。 |
    | **場所** | サービスの場所 | 最寄りの [リージョン](https://azure.microsoft.com/regions/)または関数がアクセスする他のサービスの近くのリージョン内の **[場所]** を選択します。 |
    | **[Azure Storage](storage-considerations.md)** | 汎用ストレージ アカウント | Functions Runtime には Azure Storage アカウントが必須です。 **[新規]** を選択して汎用ストレージ アカウントを構成します。 または、[ストレージ アカウントの要件](storage-considerations.md#storage-account-requirements)を満たす既存のアカウントを選択することもできます。  |

    :::image type="content" source="media/openapi-apim-integrate-vs/create-function-app-with-storage.png" alt-text="ストレージを使用して Azure で新しい関数アプリを作成する":::

1. **[作成]** を選択して、関数アプリとその関連リソースを Azure で作成します。 リソース作成のステータスがウィンドウの左下に表示されます。 

1. **[Functions instance]\(関数インスタンス\)** に戻り、 **[Run from package file]\(パッケージ ファイルから実行する\)** がオンになっていることを確認します。 関数アプリは、[Zip Deploy](functions-deployment-technologies.md#zip-deploy) を使用して、[Run-From-Package](run-functions-from-deployment-package.md) モードが有効な状態でデプロイされます。 このデプロイ方法は、パフォーマンスが向上するため、関数プロジェクトに対して推奨されます。 

1. **[次へ]** を選択し、 **[API Management]** ページで、 **[+ API Management API を作成する]** も選択します。

1.  次の表の値を使用して、**API Management で API** を作成します。

    | 設定      | 値  | 説明                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **API 名** | TurbineRepair | API の名前。 |
    | **サブスクリプション名** | 該当するサブスクリプション | 使用する Azure サブスクリプション。 このサブスクリプションを承諾するか、ドロップダウン リストから新しいものを選択します。 |
    | **リソース グループ** | リソース グループの名前 | ドロップダウン リストから、自分の関数アプリと同じリソース グループを選択します。   |
    | **API Management サービス** | 新しいインスタンス | **[新規]** を選択して、サーバーレス レベルで新しい API Management インスタンスを作成します。   |

    :::image type="content" source="media/openapi-apim-integrate-vs/create-api-management-api.png" alt-text="API Management インスタンスと API を作成する":::

1. **[作成]** を選択して、関数統合から API Management インスタンスと TurbineRepair API を作成します。

1. **[完了]** を選択し、 **[発行準備が完了しました]** と発行ページに表示されることを確認してから、 **[発行]** を選択して、プロジェクト ファイルを含むパッケージを Azure の新しい関数アプリにデプロイします。 

    デプロイが完了すると、 **[発行]** タブに Azure の関数アプリのルート URL が表示されます。 

## <a name="get-the-function-access-key"></a>関数アクセス キーを取得する

1. **[発行]** タブで、 **[ホスティング]** の横にある省略記号 ( **...** ) を選択し、 **[Azure portal で開く]** を選択します。 作成した関数アプリが、Azure portal で既定のブラウザーにより開かれます。 

1. **[関数]** で、 **[関数]**  >  **[TurbineRepair]** を選択し、 **[関数キー]** を選択します。 

    :::image type="content" source="media/openapi-apim-integrate-vs/get-function-keys.png" alt-text="TurbineRepair 関数のアクセス キーを取得する":::

1. **[関数キー]** で、 **[既定値]** を選択し、その **[値]** をコピーします。 これで、このキーを API Management に設定できます。これにより、関数エンドポイントにアクセスできるようになります。

## <a name="configure-api-management"></a>API Management を構成する

1. **[発行]** タブで、 **[ホスティング]** の横にある省略記号 ( **...** ) を選択し、 **[Azure portal で API を開く]** を選択します。 作成した API Management インスタンスが、Azure portal で既定のブラウザーにより開かれます。 この API Management インスタンスは既に関数アプリにリンクされています。 

1. **[API]** で、 **[Azure Functions OpenAPI Extension]\(Azure Functions OpenAPI 拡張機能\)**  >  **[テスト]**  >  **[POST Run]\(POST 実行\)** を選択し、 **[Inbound policy]\(受信ポリシー\)** で **[ポリシーの追加]** を選択します。

    :::image type="content" source="media/openapi-apim-integrate-vs/apim-add-policy.png" alt-text="API に受信ポリシーを追加する":::

1. **[Add inbound policy]\(受信ポリシーの追加\)** で **[クエリ パラメーターの設定]** を選択し、 **[名前]** に「`code`」と入力し、 **[+Value]\(+ 値\)** を選択し、コピーした関数キーを貼り付け、 **[保存]** を選択します。 API Management が関数エンドポイントに呼び出しを渡す際に、関数キーが含まれます。 

関数キーが設定されたので、API を呼び出して、それが Azure でホストされているときに動作することを確認できます。

## <a name="verify-the-api-in-azure"></a>Azure で API を確認する

1. API で、 **[テスト]** タブ、 **[POST Run]\(POST 実行\)** の順に選択し、 **[要求本文]**  >  **[未フォーマット]** に次のコードを入力して、 **[送信]** を選択します。

    ```json
    {
        "hours": "6",
        "capacity": "2500"
    }
    ```

    :::image type="content" source="media/openapi-apim-integrate-vs/api-management-test-function-api.png" alt-text="API Management API の OpenAPI テスト ページ":::

    前と同様に、クエリ パラメーターと同じ値を指定することもできます。 

1. **[送信]** を選択し、 **[HTTP 応答]** を表示して、API から同じ結果が返されることを確認します。

## <a name="download-the-openapi-definition"></a>OpenAPI 定義をダウンロードする

API が意図したとおりに動作する場合は、OpenAPI 定義をダウンロードできます。

1. 1. **[API]** で、 **[Azure Functions OpenAPI Extension]\(Azure Functions OpenAPI 拡張機能\)** を選択し、省略記号 ( **...** ) を選択し、 **[エクスポート]** を選択します。
   
   ![OpenAPI 定義のダウンロード](media/openapi-apim-integrate-vs/download-definition.png)

2. さまざまな形式の OpenAPI ファイルなど、API をエクスポートする手段を選択します。 [Azure API Management から Power Platform に API をエクスポート](../api-management/export-api-power-platform.md)することもできます。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

前の手順では、リソース グループ内に Azure リソースを作成しました。 これらのリソースが将来必要になると思わない場合は、リソース グループを削除してリソースを削除できます。
 
Azure portal メニューまたは **[ホーム]** ページから、 **[リソース グループ]** を選択します。 次に、 **[リソース グループ]** ページで、作成したグループを選択します。

**[myResourceGroup]** ページで、一覧表示されたリソースが、削除しようとするリソースであることを確認します。

**[リソース グループの削除]** を選択し、確認のためテキスト ボックスにグループの名前を入力して、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

Visual Studio 2019 を使用して、[OpenAPI 拡張機能](https://github.com/Azure/azure-functions-openapi-extension)により自己文書化された関数を作成し、API Management と統合しました。 ポータルの API Management で定義を編集できるようになります。 また、[API Management についてさらに学習する](../api-management/api-management-key-concepts.md)こともできます。

> [!div class="nextstepaction"]
> [API Management で OpenAPI 定義を編集する](../api-management/edit-api.md)
