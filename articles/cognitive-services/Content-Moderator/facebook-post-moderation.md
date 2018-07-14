---
title: Azure Content Moderator による Facebook コンテンツのモデレーション | Microsoft Docs
description: 機械学習ベースの Content Moderator を使用して Facebook ページをモデレートする
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
ms.openlocfilehash: 316c7212c30e9fe1151cdf5ceabf875439ad4c65
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373384"
---
# <a name="facebook-content-moderation-with-content-moderator"></a>Content Moderator による Facebook コンテンツのモデレーション

このチュートリアルでは、機械学習ベースの Content Moderator を使用して Facebook の投稿とコメントをモデレートする方法について説明します。

このチュートリアルでは、以下の手順について説明します。

1. Content Moderator チームを作成します。
2. Content Moderator と Facebook からの HTTP イベントをリッスンする Azure Functions を作成します。
3. Facebook ページとアプリを作成し、Content Moderator に接続します。

完了すると、訪問者が投稿したコンテンツは Facebook から Content Moderator に送信されるようになります。 一致するしきい値に基づいて、Content Moderator のワークフローは、コンテンツを公開するか、レビュー ツール内にレビューを作成します。 

次の図は、ソリューションの構成要素を示しています。

![Facebook 投稿のモデレート](images/tutorial-facebook-moderation.png)

## <a name="create-a-content-moderator-team"></a>Content Moderator チームを作成する

Content Moderator にサインアップしてチームを作成する方法については、[クイック スタート](quick-start.md)のページを参照してください。

## <a name="configure-image-moderation-workflow-threshold"></a>画像モデレーション ワークフロー (しきい値) を構成する

カスタム画像ワークフロー (しきい値) を構成するには、[[Workflows]\(ワークフロー\)](review-tool-user-guide/workflows.md) ページを参照してください。 ワークフローの**名前**をメモします。

## <a name="3-configure-text-moderation-workflow-threshold"></a>手順 3.テキスト モデレーション ワークフロー (しきい値) を構成する

[[Workflows]\(ワークフロー\)](review-tool-user-guide/workflows.md) ページと同様の手順を使用して、カスタム テキストのしきい値とワークフローを構成します。 ワークフローの**名前**をメモします。

![テキスト ワークフローを構成する](images/text-workflow-configure.PNG)

[Execute Workflow]\(ワークフローの実行\) ボタンを使用してワークフローをテストします。

![テキスト ワークフローのテスト](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Azure Functions を作成する

[Azure 管理ポータル](https://portal.azure.com/)にサインインして、Azure Functions を作成します。 次の手順に従います。

1. [[Azure Functions]](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal) ページの表示に従って Azure Function アプリを作成します。
2. 新しく作成した Function アプリを開きます。
3. このアプリ内で、**[Platform features]\(プラットフォームの機能\)、[Application Settings]\(アプリケーションの設定\)** の順に移動します。
4. 次の[アプリケーション設定](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings)を定義します。

> [!NOTE]
> **cm: Region** には、リージョン名を (スペースなしで) 指定する必要があります。
> たとえば、"West Europe" (西ヨーロッパ) ではなく **westeurope**、"West Central US" (アメリカ西中央ではなく) **westcentralus** などと指定します。
>

| アプリ設定 | 説明   | 
| -------------------- |-------------|
| cm:TeamId   | Content Moderator のチーム ID  | 
| cm:SubscriptionKey | Content Moderator のサブスクリプション キー - [資格情報](/review-tool-user-guide/credentials.md)に関するページを参照してください | 
| cm:Region | Content Moderator のリージョン名 (スペースなし)。 前の注を参照してください。 |
| cm:ImageWorkflow | 画像に対して実行するワークフローの名前 |
| cm:TextWorkflow | テキストに対して実行するワークフローの名前 |
| cm:CallbackEndpoint | このガイドで後で作成する CMListener Function アプリの URL |
| fb:VerificationToken | シークレット トークン。Facebook のフィード イベントにサブスクライブするためにも使用されます |
| fb:PageAccessToken | Facebook のグラフ API アクセス トークンは無期限なので、代理で投稿の非表示/削除関数を実行させることができます。 |

5. **FB Listener** という名前の新しい **HttpTrigger-CSharp** 関数を作成します。 この関数は Facebook からイベントを受け取ります。 次の手順でこの関数を作成します。

    1. 参照するために[Azure Functions の作成](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal)に関するページを開いたままにしておきます。
    2. **+** をクリックして新しい関数を作成します。
    3. 組み込みのテンプレートではなく、**[Get started on your own/custom function]\(独自/カスタムの関数を作成する\)** オプションを選択します。
    4. **HttpTrigger-CSharp** というタイルをクリックします。
    5. 「**FBListener**」という名前を入力します。 **[Authorization Level]\(承認レベル\)** フィールドは **[Function]\(関数\)** に設定する必要があります。
    6. **Create** をクリックしてください。
    7. **run.csx** の内容を [**FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx) の内容で置き換えます。

6. **CMListener** という名前の新しい **HttpTrigger-CSharp** 関数を作成します。 この関数は Facebook からイベントを受け取ります。 次の手順でこの関数を作成します。

    1. 参照するために[Azure Functions の作成](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal)に関するページを開いたままにしておきます。
    2. **+** をクリックして新しい関数を作成します。
    3. 組み込みのテンプレートではなく、**[Get started on your own/custom function]\(独自/カスタムの関数を作成する\)** オプションを選択します。
    4. **HttpTrigger-CSharp** というタイルをクリックします。
    5. 「**CMListener**」という名前を入力します。 **[Authorization Level]\(承認レベル\)** フィールドは **[Function]\(関数\)** に設定する必要があります。
    6. **Create** をクリックしてください。
    7. **run.csx** の内容を [**CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx) の内容で置き換えます。

## <a name="configure-the-facebook-page-and-app"></a>Facebook ページとアプリを構成する
1. Facebook アプリを作成します。

    1. [Facebook 開発者向けサイト](https://developers.facebook.com/)に移動します。
    2. **[My Apps]\(マイ アプリ\)** をクリックします。
    3. 新しいアプリを追加します。
    4. **[Webhooks]\(Webhook\)、[Get Started]\(始める\)** の順に選択します。
    5. **[Page]\(ページ\)、[Subscribe to this topic]\(このトピックにサブスクライブ\)** の順に選択します。
    6. [Callback URL]\(コールバック URL\) に **FBListener Url** を入力し、**[Function App Settings]\(Function アプリの設定\)** で構成した**トークンを確認**します。
    7. サブスクライブが完了したら、フィードまで下にスクロールして、**[Subscribe]\(サブスクライブ\)** を選択します。

2. Facebook ページを作成します。

    1. [Facebook](https://www.facebook.com/bookmarks/pages) に移動し、**新しい Facebook ページ**を作成します。
    2. Facebook アプリがこのページにアクセスできるようにするには、次の手順を実行します。
        1. [Graph API Explorer](https://developers.facebook.com/tools/explorer/) に移動します。
        2. **[Application]\(アプリケーション\)** を選択します。
        3. **[Page Access Token]\(ページ アクセス トークン\)** を選択し、**Get** 要求を送信します。
        4. 応答の **[Page ID]\(ページ ID\)** をクリックします。
        5. URL に **/subscribed_apps** を付加し、**Get** (空の応答) 要求を送信します。
        6. **Post** 要求を送信します。 **success: true** という応答を受け取ります。

3. 無期限の Graph API アクセス トークンを作成します。

    1. [Graph API Explorer](https://developers.facebook.com/tools/explorer/) に移動します。
    2. **[Application]\(アプリケーション\)** オプションを選択します。
    3. **[Get User Access Token]\(ユーザー アクセス トークンの取得\)** オプションを選択します。
    4. **[Select Permissions]\(アクセス許可の選択\)** で **manage_pages** および **publish_pages** オプションを選択します。
    5. この**アクセス トークン** (短命トークン) は、次の手順で使用します。

4. 次のいくつかの手順では Postman を使用します。

    1. **Postman** を開きます (または[こちら](https://www.getpostman.com/)で入手します)。
    2. 以下の 2 つのファイルをインポートします。
        1. [Postman コレクション](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman 環境](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. 以下の環境変数を更新します。
    
    | キー | 値   | 
    | -------------------- |-------------|
    | appId   | Facebook アプリ識別子をここに挿入します  | 
    | appSecret | Facebook アプリのシークレットをここに挿入します | 
    | short_lived_token | 前の手順で生成した短命のユーザー アクセス トークンを挿入します |
    4. コレクションに含まれている 3 つの API を実行します。 
        1. **[Generate Long-Lived Access Token]\(長命のアクセス トークンの生成\)** を選択し、**[Send]\(送信\)** をクリックします。
        2. **[Get User ID]\(ユーザー ID の取得\)** を選択し、**[Send]\(送信\)** をクリックします。
        3. **[Get Permanent Page Access Token]\(永続的なページ アクセス トークンの取得\)** を選択し、**[Send]\(送信\)** をクリックします。
    5. 応答から **access_token** の値をコピーし、それをアプリ設定 **fb: PageAccessToken** に割り当てます。

これで完了です。

このソリューションは Facebook ページに投稿されたすべての画像とテキストを Content Moderator に送信します。 前に構成したワークフローが呼び出されます。 ワークフローに定義されている条件を満たさないコンテンツは、レビュー ツール内のレビューに追加されます。 その他のコンテンツは公開されます。

## <a name="license"></a>ライセンス

すべての Microsoft Cognitive Services SDK およびサンプルには、MIT ライセンスがあります。 詳細については、[ライセンス](https://microsoft.mit-license.org/)に関するページを参照してください。

## <a name="developer-code-of-conduct"></a>開発者の倫理規定

このクライアント ライブラリとサンプルを含め、Cognitive Services を使用する開発者は、「Developer Code of Conduct for Microsoft Cognitive Services」(Microsoft Cognitive Services の開発者の倫理規定) (http://go.microsoft.com/fwlink/?LinkId=698895) に従うものとします。

## <a name="next-steps"></a>次の手順

1. Microsoft Build 2017 のこのソリューションの[デモ (ビデオ)](https://channel9.msdn.com/Events/Build/2017/T6033) をご覧ください。
1. [GitHub 上の Facebook サンプル](https://github.com/MicrosoftContentModerator/samples-fbPageModeration)
1. https://docs.microsoft.com/azure/azure-functions/functions-create-github-webhook-triggered-function
2. http://ukimiawz.github.io/facebook/2015/08/12/webhook-facebook-subscriptions/
3. http://stackoverflow.com/questions/17197970/facebook-permanent-page-access-token
