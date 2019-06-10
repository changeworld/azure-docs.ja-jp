---
title: チュートリアル:Azure Time Series Insights シングルページ Web アプリの作成 | Microsoft Docs
description: Azure Time Series Insights 環境のデータを照会してレンダリングするシングルページ Web アプリケーションを作成する方法について説明します。
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 5ee6e9aefb235feb28468798c3bd6b107f8c7c49
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244034"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>チュートリアル:Azure Time Series Insights シングルページ Web アプリの作成

このチュートリアルでは、Azure Time Series Insights データにアクセスする独自のシングルページ Web アプリケーション (SPA) を作成するプロセスについて説明します。 

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの設計
> * Azure Active Directory (Azure AD) にアプリケーションを登録する方法
> * Web アプリケーションをビルド、発行、テストする方法

> [!NOTE]
> * このチュートリアルのソース コードは、[GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial) で提供されています。
> * Time Series Insights [クライアントのサンプル アプリ](https://insights.timeseries.azure.com/clientsample)は、このチュートリアルで使用する完成したアプリを示すためにホストされています。

## <a name="prerequisites"></a>前提条件

* お持ちでない場合は、[無料の Azure サブスクリプション](https://azure.microsoft.com/free/)にサインアップしてください。

* Visual Studio の無料のコピー。 作業を開始するには、[2017 または 2019 Community バージョン](https://www.visualstudio.com/downloads/)をダウンロードしてください。

* Visual Studio 用の IIS Express、Web 配置、および Azure Cloud Services コア ツール コンポーネント。 これらのコンポーネントは、Visual Studio のインストールを変更することで追加してください。

## <a name="application-design"></a>アプリケーションの設計

Time Series Insights サンプル SPA は、このチュートリアルで使用される設計とコードのベースになります。 このコードでは、Time Series Insights JavaScript クライアント ライブラリを使用します。 Time Series Insights クライアント ライブラリによって、2 つの主な API カテゴリの抽象化が提供されます。

- **Time Series Insights Query API を呼び出すためのラッパー メソッド**: JSON ベースの式を使用して Time Series Insights データを照会するために使用できる REST API。 メソッドは、ライブラリの TsiClient.server 名前空間の下に整理されています。

- **いくつかの種類のグラフ コントロールを作成してデータを設定するためのメソッド**: Web ページでの Time Series Insights データの視覚化に使用できるメソッド。 メソッドは、ライブラリの TsiClient.ux 名前空間の下に整理されています。

このチュートリアルでは、サンプル アプリケーションの Time Series Insights 環境からのデータも使用します。 Time Series Insights サンプル アプリケーションの構造と Time Series Insights クライアント ライブラリの使用方法の詳細については、チュートリアル「[Azure Time Series Insights JavaScript クライアント ライブラリを調べる](tutorial-explore-js-client-lib.md)」を参照してください。

## <a name="register-the-application-with-azure-ad"></a>Azure AD へのアプリケーションの登録

ビルドする前に、アプリケーションを Azure AD に登録する必要があります。 登録によって ID 構成が行われるため、アプリケーションでシングル サインオンの OAuth サポートを使用できます。 OAuth を利用するには、SPA で暗黙的な承認付与タイプが使用される必要があります。 この承認はアプリケーション マニフェストで更新します。 アプリケーション マニフェストは、アプリケーションの ID 構成の JSON 表現です。

1. Azure サブスクリプション アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。  
1. **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新しいアプリケーションの登録]** を選択します。

   [![Azure portal - Azure AD アプリケーション登録の開始](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. **[作成]** ウィンドウで、必須のパラメーターを入力します。

   パラメーター|説明
   ---|---
   **Name** | わかりやすい登録名を入力します。  
   **アプリケーションの種類** | **[Web アプリ/API]** のままにします。
   **サインオン URL** | アプリケーションのサインイン (ホーム) ページの URL を入力します。 アプリケーションは後で Azure App Service でホストされるため、https:\//azurewebsites.net ドメイン内の URL を使用する必要があります。 この例では、名前は登録名に基づきます。

   **[作成]** をクリックして、新しいアプリケーション登録を作成します。

   [![Azure portal - Azure AD アプリケーション登録ウィンドウの作成オプション](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. リソース アプリケーションによって、他のアプリケーションで使用できる REST API が提供されます。 これらの API も Azure AD に登録されます。 API では、"*スコープ*" を公開することで、クライアント アプリケーションによるきめ細かい安全なアクセスが実現します。 アプリケーションが Azure Time Series Insights API を呼び出すため、API とスコープを指定する必要があります。 アクセス許可は、API とスコープに実行時に付与されます。 **[設定]**  >  **[必要なアクセス許可]**  >  **[追加]** の順に選択します。

   [![Azure portal - Azure AD のアクセス許可を追加するための追加オプション](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. **[API アクセスの追加]** ウィンドウで **1. の [API を選択します]** を選択して、Azure Time Series Insights API を指定します。 **[API を選択します]** ウィンドウで検索フィールドに「**azure time**」と入力します。 次に、結果リストの **[Azure Time Series Insights]** を選択します。 **[選択]** を選択します。

   [![Azure portal - Azure AD のアクセス許可を追加するための検索オプション](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. API のスコープを選択するには、 **[API アクセスの追加]** ウィンドウで、**2. の [アクセス許可を選択します]** を選択します。 **[アクセスの有効化]** ウィンドウで、 **[Access Azure Time Series Insights service](Azure Time Series Insights サービスへのアクセス)** スコープを選択します。 **[選択]** を選択します。 **[API アクセスの追加]** ウィンドウに戻ります。 **[完了]** を選択します。

   [![Azure portal - Azure AD のアクセス許可を追加するためのスコープの設定](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. **[必要なアクセス許可]** ウィンドウに、Azure Time Series Insights API が表示されるようになります。 さらに、すべてのユーザーに関して、アプリケーションが API とスコープにアクセスするためのアクセス許可を事前承認する必要があります。 **[アクセス許可の付与]** を選択し、 **[はい]** を選択します。

   [![Azure portal - Azure AD に必要なアクセス許可を追加するためのアクセス許可の付与オプション](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. 前述のとおり、アプリケーション マニフェストを更新することも必要です。 ウィンドウ上部の水平メニュー ("階層リンク") で、アプリケーション名を選択して、 **[登録済みのアプリ]** ウィンドウに戻ります。 **[マニフェスト]** を選択して `oauth2AllowImplicitFlow` プロパティを `true` に変更し、 **[保存]** を選択します。

   [![Azure portal - Azure AD マニフェストの更新](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. 階層リンクのアプリケーション名を選択して、 **[登録済みのアプリ]** ウィンドウに戻ります。 **ホーム ページ**の値とアプリケーションの**アプリケーション ID** をコピーします。 これらのプロパティを、このチュートリアルで後ほど使用します。

   [![Azure portal - アプリケーションのホーム ページの URL とアプリケーション ID の値のコピー](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>Web アプリケーションのビルドと発行

1. アプリケーションのプロジェクト ファイルを格納するディレクトリを作成します。 次に、以下の各 URL にアクセスします。 ページの右上隅にある **[Raw]** リンクを右クリックしてから、 **[名前を付けて保存]** を選択してプロジェクト ディレクトリにファイルを保存します。

   - [*index.html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html):ページの HTML と JavaScript
   - [*sampleStyles.css*]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): CSS スタイル シート

   > [!NOTE]
   > ブラウザーによっては、ファイルを保存する前にファイル拡張子を .html または .css に変更する必要がある場合があります。

1. Visual Studio に必要なコンポーネントがインストールされていることを確認します。 Visual Studio 用の IIS Express、Web 配置、および Azure Cloud Services コア ツール コンポーネントをインストールする必要があります。

    [![Visual Studio - インストールされているコンポーネントの変更](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Visual Studio のエクスペリエンスは、バージョンと構成設定によっては、示されている例とは若干異なる可能性があります。

1. Visual Studio を開いて、サインインします。 Web アプリケーション用のプロジェクトを作成するには、 **[ファイル]** メニューで **[開く]**  >  **[Web サイト]** の順に選択します。

    [![Visual Studio ‐ 新しいソリューションの作成](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. **[Web サイトを開く]** ウィンドウで、HTML ファイルと CSS ファイルを格納した作業ディレクトリを選択してから、 **[開く]** を選択します。

   [![Visual Studio - [ファイル] メニュー。[開く] と [Web サイト] オプションが選択された状態](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. Visual Studio の **[表示]** メニューで、 **[ソリューション エクスプローラー]** を選択します。 新しいソリューションが開きます。 これには Web サイト プロジェクト (地球アイコン) が含まれていて、ここに HTML ファイルと CSS ファイルが含まれています。

   [![Visual Studio - ソリューション エクスプローラー内の新しいソリューション](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. アプリを発行する前に、*index.html* の構成設定を変更する必要があります。

   1. コメント `"PROD RESOURCE LINKS"` の下の 3 行をコメント解除して、依存関係を DEVELOPMENT から PRODUCTION に切り替えます。 コメント `"DEV RESOURCE LINKS"` の下の 3 行をコメント アウトします。

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      依存関係は次の例のようにコメントする必要があります。

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. Azure AD アプリの登録 ID を使用するようにアプリを構成するには、`clientID` と `postLogoutRedirectUri` の値を変更して、「[Azure AD へのアプリケーションの登録](#register-the-application-with-azure-ad)」の手順 9 でコピーした **[アプリケーション ID]** と **[ホーム ページ]** の値を使用します。

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      例:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. 変更が完了したら、*index.html* を保存します。

1. Web アプリケーションを Azure アプリ サービスとして Azure サブスクリプションに発行します。  

   > [!NOTE]
   > 次の手順で示すスクリーンショットのいくつかのオプションには、Azure サブスクリプションからのデータが自動的に設定されます。 完全に読み込むには、ウィンドウごとに数秒かかる場合があります。  

   1. ソリューション エクスプローラーで、Web サイト プロジェクト ノードを右クリックして、 **[Web アプリの発行]** を選択します。  

      [![Visual Studio - ソリューション エクスプローラーの [Web アプリの発行] オプションの選択](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. **[開始]** を選択して、アプリの発行を開始します。

      [![Visual Studio - 発行プロファイル ウィンドウ](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. アプリケーションの発行に使用するサブスクリプションを選択します。 **[TsiSpaApp]** プロジェクトを選択します。 **[OK]** をクリックします。

      [![Visual Studio - 発行プロファイルのアプリ サービス ウィンドウ](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. **[発行]** を選択して Web アプリケーションをデプロイします。

      [![Visual Studio - 発行オプションと発行ログの出力](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Visual Studio の **[出力]** ウィンドウに成功した発行のログが表示されます。 配置が完了すると、Visual Studio によってブラウザー タブで Web アプリケーションが開かれ、サインインを求められます。 サインインが成功すると、Time Series Insights コントロールにデータが表示されます。

## <a name="troubleshoot"></a>トラブルシューティング  

エラー コード/状態 | 説明
---------------------| -----------
"*AADSTS50011: アプリケーションの応答アドレスが登録されていません。* " | Azure AD 登録に **[応答 URL]** プロパティがありません。 Azure AD アプリケーション登録の **[設定]**  >  **[応答 URL]** に移動します。 「[Azure AD へのアプリケーションの登録](#register-the-application-with-azure-ad)」の手順 3 で指定された**サインオン** URL が存在することを確認します。
"*AADSTS50011: The reply url specified in the request does not match the reply urls configured for the application: '\<Application ID GUID>'. (要求で指定されている応答 URL が、アプリケーションに関して構成されている応答 URL と一致しません: "<アプリケーション ID GUID>")* " | 「[Web アプリケーションのビルドと発行](#build-and-publish-the-web-application)」の手順 6 で指定された `postLogoutRedirectUri` が、Azure AD アプリケーション登録の **[設定]**  >  **[応答 URL]** で指定されている値に一致する必要があります。 また、 **[宛先 URL]** の値も、「[Web アプリケーションのビルドと発行](#build-and-publish-the-web-application)」の手順 5 で指定した *https* を使用するように変更してください。
Web アプリケーションによって読み込みが行われますが、背景が白く、スタイルが適用されていないテキストのみのサインイン ページが表示されます。 | 「[Web アプリケーションのビルドと発行](#build-and-publish-the-web-application)」の手順 4 で説明されているパスが正しいことを確認します。 Web アプリケーションが .css ファイルを検出できない場合、スタイルがページに正しく適用されません。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成された Azure サービスがいくつか実行されています。 このチュートリアル シリーズを完了する予定がない場合は、不要なコストが発生しないようにすべてのリソースを削除することをお勧めします。

Azure portal の左側のメニューで、次の手順を実行します。

1. **[リソース グループ]** を選択し、Time Series Insights 環境に作成したリソース グループを選択します。 ページの上部で **[リソース グループの削除]** を選択し、リソース グループの名前を入力してから **[削除]** を選択します。
1. **[リソース グループ]** を選択し、デバイス シミュレーション ソリューション アクセラレータによって作成されたリソース グループを選択します。 ページの上部で **[リソース グループの削除]** を選択し、リソース グループの名前を入力してから **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * アプリケーションの設計
> * Azure AD にアプリケーションを登録する方法
> * Web アプリケーションをビルド、発行、テストする方法

このチュートリアルでは、Azure AD との統合を行い、サインインしているユーザーの ID を使用してアクセス トークンを取得します。 サービスまたはデーモン アプリケーションの ID を使用して Time Series Insights API にアクセスする方法については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Time Series Insights API の認証と認可](time-series-insights-authentication-and-authorization.md)
