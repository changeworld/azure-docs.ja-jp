---
title: "Azure Functions の OpenAPI メタデータ | Microsoft Docs"
description: "Azure Functions での OpenAPI サポートの概要"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 08063960d3956ca65dd32d9fe726c366799a2087
ms.lasthandoff: 04/19/2017


---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Azure Functions でのOpenAPI 2.0 メタデータのサポート (プレビュー)
このプレビュー機能では、Function App 内で OpenAPI 2.0 (以前の Swagger) を記述し、Function App を使用してそのファイルをホスティングすることができます。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-metadata"></a>OpenAPI メタデータとは
[OpenAPI メタデータ](http://swagger.io/)では、REST API をホストしている関数を、他のさまざまなソフトウェアで使用できます。 これには、PowerApps や [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier) などのファースト パーティー製品や、[Postman](https://www.getpostman.com/docs/importing_swagger) ツールのようなサード パーティーの開発者ツールなどの[他の多くのパッケージ](http://swagger.io/tools/)が含まれます。

>[!TIP]
>特定の機能の詳細について学習するには、[概要チュートリアル](./functions-api-definition-getting-started.md)から始めて後でこのドキュメントに戻ることをお勧めします。

## <a name="enable"></a>OpenAPI 定義のサポートの有効化
* OpenAPI のすべての設定は、`API Definition (preview)` ページの Function App 設定で構成できます。
* `API defintion source` を `Function` に設定して、ホストされている OpenAPI 定義とクイック スタート定義の生成を有効にします。
  * `External URL` により、他の場所でホストされている OpenAPI 定義を自分の関数で使用できます。

## <a name="generate-defintion"></a>関数メタデータからの Swagger スケルトンの生成
テンプレートは、最初の OpenAPI 定義の作成を開始するのに適した方法です。 定義テンプレート機能では、HTTP トリガー関数ごとに、function.json 内のすべてのメタデータを使用してスパース OpenAPI 定義が作成されます。 **要求や応答のテンプレートなど、 [OpenAPI 仕様](http://swagger.io/specification/)から API に関する詳細情報を入力する必要があります。**

[手順については、この概要チュートリアルを確認してください](./functions-api-definition-getting-started.md)

### <a name="templates"></a>使用可能なテンプレート

|名前| Description |
|:-----|:-----|
|Generated Definition (生成された定義)|関数の既存のメタデータから推論可能な情報量が最大の OpenAPI 定義|

### <a name="quickstart-details"></a>生成された定義に含まれるメタデータ

次の表は、ポータル設定と、生成されたスケルトン Swagger にマップされたときの function.json 内の対応するデータを表します。

|Swagger.json|ポータル UI|Function.json|
|:----|:-----|:-----|
|[Host](http://swagger.io/specification/#fixed-fields-15)|`Function app settings` > `Go to App Service Settings` > `Overview` > `URL`|*存在しない*
|[Paths](http://swagger.io/specification/#paths-object-29)|`Integrate` > `Selected HTTP methods`|Bindings: Route
|[Path Item](http://swagger.io/specification/#path-item-object-32)|`Integrate` > `Route template`|Bindings: Methods
|[セキュリティ](http://swagger.io/specification/#security-scheme-object-112)|構成する|*存在しない*|
|operationID*|ルート + 使用できる動詞|ルート + 使用できる動詞|

\*Operation ID は PowerApps + Flow と統合する場合のみ必要です
> [!NOTE]
>  x-ms-summary は、Logic Apps、Flow、および PowerApps の表示名を示します。
>
> 詳細については、「[Customize your Swagger definition for PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)」を参照してください。

## <a name="CICD"></a>CI/CD を使用した API 定義の設定

 ソース管理から API 定義を変更するためにソース管理を有効にする前に、ポータルで API 定義ホスティングを有効にする必要があります。 次の手順に従ってください。

1. Function App 設定の `API Definition (preview)` に移動します。
  1. `API definition source` を `Function` に設定します
  1. `Generate API definition template` をクリックしてから `Save` をクリックし、後で変更するためのテンプレート定義を作成します。
  1. `API definition URL` と `key` をメモします
1. [CI/CD を設定します](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements)
2. `\site\wwwroot\.azurefunctions\swagger\swagger.json` にあるソース管理の `swagger.json` を変更します

これで、レポジトリ内での `swagger.json` への変更は、手順 1.3 に記載されている `API definition URL` と `key` の Function App によってホストされます

## <a name="next-steps"></a>次のステップ
* [概要チュートリアル](functions-api-definition-getting-started.md)
  * OpenAPI 定義が動作することを確認するチュートリアルを試してください。
* [Azure Functions GitHub リポジトリ](https://github.com/Azure/Azure-Functions/)
  * API 定義のサポート プレビューについてフィードバックを提供するには、Functions Github を参照してください。 更新が必要な内容については、Github の問題を作成してください。
* [Azure Functions 開発者向けリファレンス](functions-reference.md)  
  * 関数のコーディングとトリガーおよびバインドの定義に関するプログラマ向けリファレンスです。

