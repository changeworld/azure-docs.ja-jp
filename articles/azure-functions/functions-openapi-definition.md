---
title: Azure API Management を使用した関数の OpenAPI 定義の作成
description: 他のアプリやサービスが Azure で関数を呼び出せるようにする OpenAPI 定義を作成します。
services: functions
keywords: OpenAPI, Swagger, クラウド アプリ, クラウド サービス,
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 3ad304bc8f038d4009352dae72d70079828c26ba
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141539"
---
# <a name="create-an-openapi-definition-for-a-function-with-azure-api-management"></a>Azure API Management を使用した関数の OpenAPI 定義の作成

REST API は、多くの場合、OpenAPI 定義を使用して記述されます。 この定義には、API で使用できる操作の情報と、API の要求データと応答データを構造化する方法に関する情報が含まれています。

このチュートリアルでは、風力タービンの応急修復がコスト効率に優れているかどうかを確認する関数を作成します。 その後、その関数を他のアプリやサービスから呼び出せるように、[Azure API Management](../api-management/api-management-key-concepts.md) を使用して関数アプリの OpenAPI 定義を作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure で関数を作成する
> * Azure API Management を使用して OpenAPI 定義を生成する
> * 関数を呼び出して定義をテストする

## <a name="create-a-function-app"></a>Function App を作成する

関数の実行をホストするための Function App が存在する必要があります。 関数アプリを使用すると、リソースの管理、デプロイ、スケーリング、および共有を容易にするための論理ユニットとして関数をグループ化できます。

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>関数を作成する

このチュートリアルでは、2 つのパラメーターを受け取り、HTTP によってトリガーされる関数を使用します。

* タービン修復の推定所要時間 (時間単位)。
* タービンの容量 (キロワット単位)。 

この関数は、修復コストと、タービンによってもたらされる 24 時間あたり収益を計算します。 HTTP によってトリガーされる関数を [Azure portal](https://portal.azure.com) 内で作成します。

1. Function App を展開し、**[関数]** の横にある **[+]** ボタンを選択します。 **[ポータル内]** > **[続行]** の順に選択します。

1. **[その他のテンプレート]** を選択した後、**[テンプレートの完了と表示]** を選択します。

1. [HTTP トリガー] を選択し、関数の**名前**として「`TurbineRepair`」と入力し、**[[認証レベル]](functions-bindings-http-webhook.md#http-auth)** で [`Function`] を選択し、**[作成]** を選択します。  

    ![OpenAPI 用の HTTP 関数を作成する](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. この run.csx C# スクリプト ファイルの内容を次のコードに置き換えて、**[保存]** を選択します。

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    この関数コードは、応急修復がコスト効率に優れているかを示すメッセージ `Yes` または `No` のほか、タービンによって創出される収益機会とタービンの修復コストを返します。

1. 関数をテストするには、一番右の **[テスト]** をクリックして、テスト タブを展開します。**要求本文**で次の値を入力し、**[実行]** をクリックします。

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Azure Portal で関数をテストする](media/functions-openapi-definition/test-function.png)

    応答本文で次の値が返されます。

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

これで、応急修復のコスト効率性を確認する関数が作成されました。 次は、関数アプリの OpenAPI 定義を生成します。

## <a name="generate-the-openapi-definition"></a>OpenAPI 定義を生成する

OpenAPI 定義を生成する準備ができています。

1. 関数アプリを選択してから、**[プラットフォーム機能]**、**[すべての設定]** の順に選択します。

    ![Azure Portal で関数をテストする](media/functions-openapi-definition/select-all-settings-openapi.png)

1. 下へスクロールし、**[API Management]** > **[新規作成]** の順に選択して、新しい API Management インスタンスを作成します。

    ![関数をリンクする](media/functions-openapi-definition/link-apim-openapi.png)

1. 画像の下の表で指定されている API Management の設定を使用してください。

    ![新しい API Management サービスを作成する](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Setting      | 推奨値  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | グローバルに一意の名前 | お使いの関数アプリの名前に基づいて名前が生成されます。 |
    | **サブスクリプション** | 該当するサブスクリプション | この新しいリソースが作成されるサブスクリプション。 |  
    | **[リソース グループ](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | お使いの関数アプリと同じリソース。自動的に設定されます。 |
    | **場所** | 米国西部 | 場所には [米国西部] を選びます。 |
    | **組織名** | Contoso | 開発者ポータルとメール通知で使用する組織の名前。 |
    | **管理者のメール アドレス** | ご自分のメール アドレス | API Management からのシステム通知を受信したメール アドレス。 |
    | **[価格レベル]** | Consumption (プレビュー) | 価格の詳細については、[API Management の価格に関するページ](https://azure.microsoft.com/pricing/details/api-management/)を参照してください。 |
    | **Application Insights** | お使いのインスタンス | お使いの関数アプリが使用しているのと同じ Application Insights を使用します。 |

1. **[作成]** を選択して、API Management インスタンスを作成します。これには数分かかる場合があります。

1. **[Application Insights を有効にする]** を選択して、関数アプリケーションと同じ場所にログを送信します。次に、残りの既定値をそのまま使用し、**[API のリンク]** を選択します。

1. **[Azure Functions のインポート]** が開き、**TurbineRepair** 関数が強調表示されます。 **[選択]** を選択して続行します。

    ![API Management に Azure Functions をインポートする](media/functions-openapi-definition/import-function-openapi.png)

1. **[Function App から作成する]** ページで、既定値をそのまま使用して **[作成]** を選択します。

    ![Function App から作成する](media/functions-openapi-definition/create-function-openapi.png)

これで、関数の API が作成されました。

## <a name="test-the-openapi-definition"></a>OpenAPI 定義をテストする

API 定義を使用する前に、それが動作することを確認する必要があります。

1. お使いの関数の **[テスト]** タブで、**POST** 操作を選択します。

1. **hours** と **capacity** の値を入力します。

```json
{
"hours": "6",
"capacity": "2500"
}
```

1. **[送信]** をクリックして HTTP 応答を確認します。

    ![関数 API をテストする](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure で関数を作成する
> * Azure API Management を使用して OpenAPI 定義を生成する
> * 関数を呼び出して定義をテストする

次のトピックに進み、API Management について確認してください。

> [!div class="nextstepaction"]
> [API Management](../api-management/api-management-key-concepts.md)
