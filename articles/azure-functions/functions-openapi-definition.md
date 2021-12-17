---
title: Azure API Management を使用して、関数から API を公開する
description: 他のアプリやサービスが Azure で関数を呼び出せるようにする OpenAPI 定義を作成します。
ms.topic: how-to
ms.date: 05/04/2021
ms.openlocfilehash: ec25b03b3d93fcd69297899653279fea86eabb02
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110375667"
---
# <a name="expose-serverless-apis-from-http-endpoints-using-azure-api-management"></a>Azure API Management を使用して HTTP エンドポイントからサーバーレス API を公開する

ポータル内で Azure Functions を Azure API Management と統合し、HTTP トリガー関数エンドポイントを REST API として公開できます。 これらの API は、OpenAPI 定義を使用して記述されます。 この JSON (または YAML) ファイルには、API で使用できる操作に関する情報が含まれています。 これには、API の要求と応答のデータを構造化する方法に関する詳細が含まれます。 関数アプリを統合することで、API Management はこれらの OpenAPI 定義を生成することができます。  

この記事には、関数アプリと API Management を統合する方法が記載されています。 この統合は、[サポートされている任意の言語](supported-languages.md)で開発された関数アプリに対して機能します。 また、[Azure API Management から関数アプリをインポートする](../api-management/import-function-app-as-api.md)こともできます。

C# クラス ライブラリ関数の場合、[Visual Studio を使用](openapi-apim-integrate-visual-studio.md)して API Management と統合するサーバーレス API を作成および発行することもできます。  

## <a name="create-the-api-management-instance"></a>API Management インスタンスを作成する

関数アプリにリンクする API Management インスタンスを作成するには:

1. 関数アプリを選択した後、左側のメニューから **[API Management]** を選択し、 **[API Management]** で **[新規作成]** を選択します。

    :::image type="content" source="media/functions-openapi-definitions/select-all-settings-openapi.png" alt-text="[API Management] を選択する":::


1. 次の表に指定されている API Management の設定を使用してください。

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | グローバルに一意の名前 | お使いの関数アプリの名前に基づいて名前が生成されます。 |
    | **サブスクリプション** | 該当するサブスクリプション | この新しいリソースが作成されるサブスクリプション。 |  
    | **[リソース グループ](../azure-resource-manager/management/overview.md)** |  myResourceGroup | お使いの関数アプリと同じリソース。自動的に設定されます。 |
    | **場所** | サービスの場所 | 関数アプリと同じ場所を選択してください。 |
    | **組織名** | Contoso | 開発者ポータルとメール通知で使用する組織の名前。 |
    | **管理者のメール アドレス** | ご自分のメール アドレス | API Management からのシステム通知を受信したメール アドレス。 |
    | **価格レベル** | 従量課金 | 従量課金レベルはすべてのリージョンで利用できるわけではありません。 価格の詳細については、[API Management の価格に関するページ](https://azure.microsoft.com/pricing/details/api-management/)を参照してください。 |

    ![新しい API Management サービスを作成する](media/functions-openapi-definitions/new-apim-service-openapi.png)

1. **[作成]** を選択して、API Management インスタンスを作成します。これには数分かかる場合があります。

1. Azure によってインスタンスが作成されると、ページ上の **[Application Insights を有効にする]** オプションが有効になります。 これを選択してログを関数アプリと同じ場所に送信します。

## <a name="import-functions"></a>関数のインポート

API Management インスタンスが作成されると、HTTP によってトリガーされる関数エンドポイントをインポートできます。 この例では、TurbineRepair という名前のエンドポイントをインポートします。   

1. [API Management] ページで、 **[Link API]\(API をリンク\)** を選択します。

1. **[Azure Functions のインポート]** が開き、**TurbineRepair** 関数が強調表示されます。 **[選択]** を選択して続行します。

    ![API Management に Azure Functions をインポートする](media/functions-openapi-definitions/import-function-openapi.png)

1. **[関数アプリから作成する]** ページで、既定値をそのまま使用して **[作成]** を選択します。

    :::image type="content" source="media/functions-openapi-definitions/create-function-openapi.png" alt-text="[関数アプリから作成する]":::

    Azure によって、関数の API が作成されます。

## <a name="download-the-openapi-definition"></a>OpenAPI 定義をダウンロードする

関数をインポートした後、OpenAPI 定義を API Management インスタンスからダウンロードすることができます。

1. ページの上部にある **[OpenAPI 定義をダウンロードする]** を選択します。
   
   ![OpenAPI 定義のダウンロード](media/functions-openapi-definitions/download-definition.png)

2. ダウンロードした JSON ファイルを保存し、これを開きます。 定義を確認します。

## <a name="next-steps"></a>次のステップ

ポータルの API Management で定義を編集できるようになります。 また、[API Management についてさらに学習する](../api-management/api-management-key-concepts.md)こともできます。

> [!div class="nextstepaction"]
> [API Management で OpenAPI 定義を編集する](../api-management/edit-api.md)
