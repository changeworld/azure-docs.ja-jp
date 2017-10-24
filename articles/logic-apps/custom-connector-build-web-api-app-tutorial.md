---
title: "Web API からのカスタム コネクタの作成 - Azure Logic Apps | Microsoft Docs"
description: "Web API からカスタム コネクタを構築します"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 22bf335718721d29933557142b436e75778f8c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-connectors-from-web-apis"></a>Web API からのカスタム コネクタの作成

Azure Logic Apps、Microsoft Flow、または Microsoft PowerApps で使用できるカスタム コネクタを構築するには、Azure Web Apps でホストできる Web API を最初に作成し、Azure Active Directory に対して認証し、Logic Apps、Flow、または PowerApps にそれをコネクタとして登録します。 このチュートリアルでは、ASP.NET Web API アプリを構築して、これらのタスクを実行する方法を説明します。 コネクタのトリガーとアクションのコードを構造化できるパターンについては、「[Create custom APIs that you can call from logic app workflows (ロジック アプリ ワークフローから呼び出すことができるカスタム API を作成する)](../logic-apps/logic-apps-create-api-app.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2013 以降](https://www.visualstudio.com/vs/)。 このチュートリアルでは、Visual Studio 2015 を使用します。

* Web API のコード。 利用できるコードがない場合は、「[Getting Started with ASP.NET Web API 2 (C#) (ASP.NET Web API 2 (C#) 入門)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)」のチュートリアルを試してください。

* Azure サブスクリプション。 サブスクリプションがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)を使用できます。 また、[従量課金のサブスクリプション](https://azure.microsoft.com/pricing/purchase-options/)にサインアップすることもできます。

## <a name="create-and-deploy-an-aspnet-web-app-to-azure"></a>ASP.NET Web アプリを作成し、Azure にデプロイする

このチュートリアルでは、Visual C# ASP.NET Web アプリケーションを作成します。 

1. Visual Studio を開き、**[ファイル]** > **[新しいプロジェクト]** の順に選択します。

   1. **[Installed]\(インストール済み\)** を展開します。**[テンプレート]** > **[Visual C#]** > **[Web]** の順に選択し、**[ASP.NET Web アプリケーション]** を選択します。

   2. アプリのプロジェクト名、場所、およびソリューション名を入力し、**[OK]** を選択します。

   For example:

   ![Visual C# ASP.NET Web アプリケーションを作成する](./media/custom-connector-build-web-api-app-tutorial/visual-studio-new-project-aspnet-web-app.png)

2. **[新しい ASP.NET Web アプリケーション]** ボックスで、**Web API** テンプレートを選択します。 **[クラウドにホストする]** がオンになっていない場合はこれをオンにします。 **[認証の変更]** を選択します。

   !["Web API" テンプレート、[クラウドにホストする]、および [認証の変更] を選択する](./media/custom-connector-build-web-api-app-tutorial/visual-studio-web-api-template.png)

3. **[認証なし]** を選択し、**[OK]** を選択します。 認証は後で設定することができます。

   ![[認証なし] を選択する](./media/custom-connector-build-web-api-app-tutorial/visual-studio-change-authentication.png)

4. **[新しい ASP.NET Web アプリケーション]** ボックスが再び表示されたら、**[OK]** を選択します。 

5. **[App Service の作成]** ボックスで、表に示されているホスティング設定を確認したうえで必要な変更を加え、**[作成]** を選択します。 

   [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)は、Azure サブスクリプションでアプリをホストするために使用される物理リソースのコレクションを表しています。 [App Service](../app-service/app-service-value-prop-what-is.md) についての詳細情報。

   ![App Service を作成する](./media/custom-connector-build-web-api-app-tutorial/visual-studio-create-app-service.png)

   | 設定 | 推奨値 | Description | 
   | ------- | --------------- | ----------- | 
   | Azure の職場または学校アカウント、または個人の Microsoft アカウント | <*ユーザー アカウント*> | ユーザー アカウントを選択します。 | 
   | **[Web アプリの名前]** | *<カスタムの Web API アプリ名>* または既定の名前 | たとえば、http://*<Web API アプリ名>* のように、アプリの URL に使用されている Web API アプリの名前を入力します。 | 
   | **サブスクリプション** | <*Azure サブスクリプション名*> | 使用する Azure サブスクリプションを選択します。 | 
   | **リソース グループ** | <*Azure リソース グループ名*> | 既存の Azure リソース グループを選択するか、リソース グループをまだ作成していない場合は作成します。 <p>**注**: Azure リソース グループを使用して、Azure サブスクリプションの Azure リソースを整理します。 | 
   | **App Service プラン** | *<App Service プラン名>* | 既存の App Service プランを選択するか、プランをまだ作成していない場合は作成します。 | 
   |||| 

   App Service プランを作成する場合は、次の設定を指定します。

   | 設定 | 推奨値 | Description | 
   | ------- | --------------- | ----------- | 
   | **場所** | *<デプロイ リージョン>* | アプリをデプロイするリージョンを選択します。 | 
   | **サイズ** | *<App Service プラン サイズ>* | プランのサイズを選択します。これにより、サービス プランのコストとコンピューティング リソース容量が決まります。 | 
   |||| 

   アプリに必要な他のリソースを設定するには、**[Explore additional Azure services]\(ほかの Azure サービスを探す\)** を選択します。

   | 設定 | 推奨値 | Description | 
   | ------- | --------------- | ----------- | 
   | **リソースの種類** | *<Azure リソース タイプ>* | アプリで必要な追加のリソースを選択して設定します。 | 
   |||| 

6. Visual Studio でプロジェクトを配置した後、アプリのコードをビルドします。

## <a name="create-an-openapi-swagger-file-that-describes-your-web-api"></a>Web API を記述する OpenAPI (Swagger) ファイルを作成する

Web API アプリを Logic Apps に接続するには、API の操作を説明する [OpenAPI (旧 Swagger) ファイル](http://swagger.io/)が必要です。 API の独自の OpenAPI 定義は [Swagger オンライン エディター](http://editor.swagger.io/)で作成できますが、このチュートリアルでは [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md) という名前のオープン ソースのツールを使用します。

1. Visual Studio プロジェクトに Swashbuckle Nuget パッケージをまだインストールしていない場合はインストールします。

   1. Visual Studio で、**[ツール]**>**[NuGet パッケージ マネージャー]**> 
   **[Package Manager Console]\(パッケージ マネージャー コンソール\)** の順に選択します。

   2. **パッケージ マネージャー コンソール**で、アプリのプロジェクト ディレクトリに移動していない場合はプロジェクト ディレクトリに移動し (`Set-Location "project-path"` を実行)、次の PowerShell コマンドレットを実行します。 
   
      `Install-Package Swashbuckle`

      For example:

      ![パッケージ マネージャー コンソール。Swashbuckle をインストールする](./media/custom-connector-build-web-api-app-tutorial/visual-studio-package-manager-install-swashbuckle.png)

   > [!TIP]
   > Swashbuckle をインストールした後でアプリを実行すると、Swashbuckle によって次の URL に OpenAPI ファイルが生成されます。 
   >
   > http://*<Web API アプリのルート URL>*/swagger/docs/v1
   > 
   > さらに、Swashbuckle によって次の URL にユーザー インターフェイスが生成されます。 
   > 
   > http://*<Web API アプリのルート URL>*/swagger

3. 準備ができたら、Web API アプリを Azure に発行します。 Visual Studio から発行するには、ソリューション エクスプローラーで Web プロジェクトを右クリックし、**[Publish...]\(発行\)** を選択し、プロンプトに従います。

   > [!IMPORTANT]
   > 操作 ID が重複していると OpenAPI ドキュメントが無効になります。 サンプル C# テンプレートを使用した場合、テンプレートで `Values_Get` "*操作 ID が 2 回繰り返されます*"。 
   > 
   > この問題を解決するには、1 つのインスタンスを `Value_Get` に変更して再発行します。

4. 次の場所を参照して OpenAPI ドキュメントを取得します。 

   http://*<Web API アプリのルート URL>*/swagger/docs/v1

   また、このチュートリアルから[サンプル OpenAPI ドキュメント](https://pwrappssamples.blob.core.windows.net/samples/webAPI.json)をダウンロードすることもできます。 
   ドキュメントを使用する前に、"//" で始まるコメントを削除してください。

5. コンテンツを JSON ファイルとして保存します。 使用しているブラウザーによっては、テキストをコピーして空のテキスト ファイルに貼り付ける操作を実行する必要がある場合があります。

## <a name="next-steps"></a>次のステップ

* [カスタム コネクタの認証を設定する](../logic-apps/custom-connector-azure-active-directory-authentication.md)











