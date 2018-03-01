---
title: "カスタム コネクタを登録する - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps で使うためにカスタム コネクタをセットアップします"
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
ms.openlocfilehash: 722b82aabe796367d906e9338355b83a1a8b1e1c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2018
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>Azure Logic Apps でカスタム コネクタを登録する

REST API を作成し、認証を設定し、OpenAPI 定義ファイルまたは Postman Collection を取得すると、コネクタを登録する準備ができます。 

## <a name="prerequisites"></a>前提条件

カスタム コネクタを登録するには、次の項目が必要です。

* Azure にコネクタを登録するための詳細 (名前、Azure サブスクリプション、Azure リソース グループ、使用場所など)

* API が記述されている OpenAPI (Swagger) ファイルまたは Postman Collection

  このチュートリアルでは、[Azure Resource Manager のサンプル OpenAPI ファイル](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)を使うことができます。

* コネクタを表すアイコン

* コネクタの簡単な説明

* API のホストの場所

## <a name="1-create-your-connector"></a>1.コネクタを作成する

1. Azure Portal の Azure メイン メニューで、**[リソースの作成]** を選びます。 検索ボックスにフィルターとして「Logic Apps コネクタ」と入力し、Enter キーを押します。

   ![[新規]、"Logic Apps コネクタ" の検索](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. 結果の一覧から、**[Logic Apps コネクタ]** > **[作成]** を選びます。

   ![Logic Apps コネクタを作成する](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. 表に示すように、コネクタ登録の詳細を指定します。 終了したら、**[ダッシュボードにピン留めする]** > **[作成]** を選びます。

   ![Logic App カスタム コネクタの詳細](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | プロパティ | 推奨値 | Description | 
   | -------- | --------------- | ----------- | 
   | **名前** | <*カスタム コネクタ名*> | コネクタの名前を指定します。 | 
   | **サブスクリプション** | <*Azure サブスクリプション名*> | Azure サブスクリプションを選択します。 | 
   | **[リソース グループ]** | <*Azure リソース グループ名*> | Azure リソースを整理するための Azure グループを作成または選択します。 | 
   | **場所** | <*デプロイ リージョン*> | コネクタをデプロイするリージョンを選びます。 | 
   |||| 

   Azure がコネクタをデプロイした後、カスタム コネクタ メニューが自動的に開きます。 
   開かない場合は、Azure ダッシュボードからカスタム コネクタを選びます。

## <a name="2-define-your-connector"></a>2.コネクタを定義する

次に、コネクタを作成するための OpenAPI ファイルまたは Postman Collection、コネクタが使う認証、カスタム コネクタが提供するアクションとトリガー、アクションとトリガーが使用できるパラメーターを指定します。

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. コネクタの OpenAPI ファイルまたは Postman Collection を指定する

1. コネクタのメニューで、まだ選ばれていない場合は、**[Logic Apps コネクタ]** を選びます。 ツール バーで、**[編集]** を選びます。

   ![カスタム コネクタを編集する](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. **[全般]** を選び、以下の表に従って、カスタム コネクタのアクションとトリガーを作成、保護、定義するための詳細を指定します。

   1. **[カスタム コネクタ]** では、API が記述されている OpenAPI (Swagger) ファイルを提供するオプションを選びます。

      ![API の OpenAPI ファイルを提供する](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | オプション | 形式 |[説明] | 
      | ------ | ------ | ----------- | 
      | **OpenAPI ファイルをアップロードします** | <*OpenAPI (Swagger) の JSON ファイル*> | OpenAPI ファイルの場所を参照して、ファイルを選びます。 | 
      | **OpenAPI URL の使用** | http://<*Swagger JSON ファイルへのパス*> | API の OpenAPI ファイルの URL を指定します。 | 
      | **Postman Collection V1 のアップロード** | <*エクスポートされた Postman Ccollection V1 ファイル*> | V1 形式でエクスポートされた Postman Collection の場所を参照します。 | 
      |||| 

   2. **[全般情報]** では、コネクタ用のアイコンをアップロードします。 
   通常、**[説明]**、**[ホスト]**、**[ベース URL]** フィールドは、OpenAPI ファイルから自動的に設定されます。 
   設定されない場合は、表に示すようにこの情報を追加して、**[続行]** を選びます。 

      ![コネクタの詳細](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | オプションまたは設定 | 形式 | [説明] | 
      | ----------------- | ------ | ----------- | 
      | **アップロード アイコン** | <*1 MB 以下の PNG または JPG ファイル*> | コネクタを表すアイコン <p>色: できれば、白いロゴとカラーの背景。 <p>寸法: 230 ピクセルの四角形の中に 160 ピクセル以下のロゴ | 
      | **アイコンの背景色** | <*アイコンのブランドの色の 16 進数コード*> | <p>アイコン ファイルの背景色に一致するアイコンの背景色。 <p>形式: 16 進数。 例: #007ee5 は青を表します。 | 
      | **説明** | <*コネクタの説明*> | コネクタの簡単な説明を指定します。 | 
      | **Host** | <*コネクタのホスト*> | Web API のホスト ドメインを指定します。 | 
      | **ベース URL** | <*コネクタのベース URL*> | Web API のベース URL を指定します。 | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. コネクタで使う認証を指定する

1. **[セキュリティ]** を選び、コネクタで使う認証を確認または指定します。 認証は、ユーザーの ID がサービスとクライアントの間を適切に流れるようにします。

   ![認証の種類](./media/logic-apps-custom-connector-register/security.png)

   * OpenAPI ファイルをアップロードした場合は、登録ウィザードが Web API で使われている認証の種類を自動的に検出し、そのファイルの `securityDefinitions` オブジェクトに基づいて、ウィザードの **[セキュリティ]** セクションを自動的に設定します。 たとえば、OAuth2.0 を使って指定されたセクションを次に示します。

     ``` json
     "securityDefinitions": {
       "AAD": {
       "type": "oauth2",
       "flow": "accessCode",
       "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
       "tokenUrl": "https://login.windows.net/common/oauth2/token",
       "scopes": {}
       }
     },
     ```

   * OpenAPI ファイルで認証の種類とプロパティが設定されなかった場合は、**[編集]** を選んでこの情報を追加します。 
   
     たとえば、前に[このチュートリアルで Azure AD 認証を設定](../logic-apps/custom-connector-azure-active-directory-authentication.md)した場合は、Web API とコネクタを保護するための Azure AD アプリが作成されています。 
     ここでは、前に保存したアプリケーション ID とクライアント キーを指定できます。
    
     | Setting | 推奨値 | [説明] | 
     | ------- | --------------- | ----------- | 
     | **認証の種類** | OAuth 2.0 | | 
     | **ID プロバイダー** | Azure Active Directory | | 
     | **クライアント ID** | <*コネクタ Azure AD アプリのアプリケーション ID*> | コネクタの Azure AD アプリに対して前に保存したアプリケーション ID です | 
     | **クライアント シークレット** | <*コネクタ Azure AD アプリのクライアント キー*> | コネクタの Azure AD アプリのクライアント キーです | 
     | **ログイン URL** | `https://login.windows.net` | | 
     | **リソース URL** | `https://management.core.windows.net/` | 末尾のスラッシュを含めて、指定どおり正確に URL を追加します。 | 
     |||| 

   * サポートされている認証の種類 (OAuth 2.0、Basic など) を使うと、自動的に OpenAPI ファイルを生成する Postman Collection は、認証の種類 "*のみ*" を自動的に設定します。

2. セキュリティ情報を入力した後でコネクタを保存するには、ページの上部にある **[コネクタの更新]** を選んでから **[続行]** を選びます。 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. コネクタのアクションとトリガーを確認、更新、定義する

1. 次に、**[定義]** を選んで、ユーザーがワークフローに追加できる新しいアクションとトリガーを確認、編集、定義します。

   アクションとトリガーは、OpenAPI ファイルまたは Postman Collection で定義されている操作に基づき、**[定義]** ページが自動的に設定され、要求と応答の値が含まれます。 したがって、必要な操作が既にここに表示されている場合は、このページを変更せずに、登録プロセスの次の手順に進むことができます。

   ![コネクタの定義](./media/logic-apps-custom-connector-register/definition.png)

2. 既存のアクションとトリガーを編集するか、新しいアクションとトリガーを追加する必要がある場合は、以下の手順に進みます。

<a name="add-action-or-trigger"></a> 

#### <a name="edit-or-add-actions-for-your-connector"></a>コネクタのアクションを編集または追加する

1. 既存のアクションを編集するには、そのアクションの番号を選びます。 OpenAPI ファイルまたは Postman Collection に存在しないアクションを追加するには、**[アクション]** の **[新しいアクション]** を選びます。

2. **[全般]** で、アクションのこの情報を指定または編集します。
   
   | Setting | 推奨値 | [説明] | 
   | ------- | --------------- | ----------- | 
   | **まとめ** | <*操作名*> | このアクションのタイトルです。 | 
   | **説明** | <*操作の説明*> | このアクションの説明です。 <p>**ヒント**: 説明の最後に句点が付いていることを確認します。 |
   | **操作 ID** | <*操作の識別子*> | このアクションを識別するための一意名です。 キャメル ケースを使います (例: "GetPullRequest")。 | 
   |**表示**| **なし**、**詳細**、**内部**、**重要** | このアクションのユーザー向けの表示です。 詳しくは、[OpenAPI の拡張機能](../logic-apps/custom-connector-openapi-extensions.md#visibility)に関するページをご覧ください。 | 
   |||| 

3. 次に、アクションの要求を定義します。
  
   1. **[要求]** セクションで、**[サンプルからのインポート]** を選びます。 

   2. **[サンプルからのインポート]** ページで、サンプルの要求を貼り付けます。 

      通常、サンプルの要求は API ドキュメントのものを使用でき、**[動詞]**、**[URL]**、**[ヘッダー]**、**[本文]** の各フィールドの情報を取得できます。 たとえば、[Text Analytics API のドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)をご覧ください。

      ![サンプル要求をインポートする](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Postman Collection からコネクタを作成する場合は、アクションとトリガーから `Content-type` ヘッダーを必ず削除してください。 Logic Apps はこのヘッダーを自動的に追加します。 また、`Security` セクションで定義した認証ヘッダーもアクションとトリガーから削除します。

      OpenAPI の詳細な機能については、「[カスタム コネクタの OpenAPI 拡張機能](../logic-apps/custom-connector-openapi-extensions.md)」をご覧ください。

   3. 要求の定義を終了するには、**[インポート]** を選びます。

4. 次に、アクションの応答を定義します。

   1. **[応答]** では、既定の応答を指定できます。 
   **[既定の応答を追加する]** を選びます。

   2. **[サンプルからのインポート]** ページで、サンプルの応答を貼り付けて、**[インポート]** を選びます。

5. 最後に、**[検証]** では、アクションで特定された潜在的な問題を確認して修正します。

#### <a name="edit-or-add-triggers-for-your-connector"></a>コネクタのトリガーを編集または追加する

1. 既存のトリガーを編集するには、そのトリガーの番号を選びます。 OpenAPI ファイルまたは Postman Collection に存在しないトリガーを追加するには、**[トリガー]** の **[新しいトリガー]** を選びます。

2. **[全般]** で、トリガーのこの情報を指定または編集します。

   | Setting | 推奨値 | [説明] | 
   | ------- | --------------- | ----------- | 
   | **まとめ** | <*操作名*> | このトリガーのタイトルです。 | 
   | **説明** | <*操作の説明*> | このトリガーの説明です。 <p>**ヒント**: 説明の最後に句点が付いていることを確認します。 | 
   | **操作 ID** | <*操作の識別子*> | このトリガーを識別するための一意名です。 キャメル ケースを使います (例: "TriggerOnGitHubPushEvent")。 | 
   |**表示**| **なし**、**詳細**、**内部**、**重要** | このトリガーのユーザー向けの表示です。 詳しくは、[OpenAPI の拡張機能](../logic-apps/custom-connector-openapi-extensions.md#visibility)に関するページをご覧ください。 | 
   | **トリガーの種類** | **Webhook**、**ポーリング** | このトリガーの種類です。 たとえば、webhook トリガーは、特定のイベントが発生するのを待ってから、生成されます。 ポーリング トリガーは、指定された時間と頻度に基づいて、サービス エンドポイントを定期的にチェックします。 <p>webhook トリガーとポーリング トリガーのパターンについて詳しくは、[カスタム API の作成](../logic-apps/logic-apps-create-api-app.md)に関するページをご覧ください。 | 
   |||| 

3. 次に、トリガーを作成する要求を定義します。 

   1. **[要求]** セクションで、**[サンプルからのインポート]** を選びます。

   2. **[サンプルからのインポート]** ページで、サンプルの要求を貼り付けます。 

      通常、サンプルの要求は API ドキュメントのものを使用でき、**[動詞]**、**[URL]**、**[ヘッダー]**、**[本文]** の各フィールドの情報を取得できます。 たとえば、[Text Analytics API のドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)をご覧ください。

      ![サンプル要求をインポートする](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > Postman Collection からコネクタを作成する場合は、アクションとトリガーから `Content-type` ヘッダーを必ず削除してください。 Logic Apps はこのヘッダーを自動的に追加します。 また、`Security` セクションで定義した認証ヘッダーもアクションとトリガーから削除します。

      OpenAPI の詳細な機能については、「[カスタム コネクタの OpenAPI 拡張機能](../logic-apps/custom-connector-openapi-extensions.md)」をご覧ください。

   3. 要求の定義を終了するには、**[インポート]** を選びます。 

4. 次に、トリガーの応答を定義します。 **[応答]** セクションで、トリガーの種類に基づいて、以下の手順を行います。

   **webhook トリガー**
   1. **[webhook の応答]** で、**[サンプルからのインポート]** を選びます。 

   2. **[サンプルからのインポート]** ページで、サンプルの応答を貼り付けて、**[インポート]** を選びます。 応答の例を表示する方法については、[webhook の作成に関する GitHub API リファレンス](https://developer.github.com/v3/repos/hooks/#create-a-hook)をご覧ください。

   3. **[トリガーの構成]** で、webhook 作成要求に使うパラメーターを選びます。 Logic Apps は、このパラメーター値を使って、トリガーとの通信にサービスが使うコールバック URL を設定します。

   **ポーリング トリガー**
   1. **[応答]** では、既定の応答を指定できます。 
   **[既定の応答を追加する]** を選びます。

   2. **[サンプルからのインポート]** ページで、サンプルの応答を貼り付けて、**[インポート]** を選びます。

   3. **[トリガーの構成]** で、クエリ パラメーター、パラメーターに渡す値、次の要求の適切なポーリング間隔を指定する *[トリガーのヒント]* を指定します。

5. 最後に、**[検証]** では、トリガーで特定された潜在的な問題を確認して修正します。

#### <a name="review-reference-definitions-for-your-connector"></a>コネクタの参照定義を確認する

**[参照]** セクションは API の記述から自動的に設定され、アクションとトリガーで参照できるパラメーター フィールドが記述されています。 

1. **[参照]** で、確認する参照定義の番号を選びます。

2. **[名前]** で、参照定義の名前を確認または更新します。

3. **[検証]** では、参照定義で特定された潜在的な問題を確認して修正します。

## <a name="3-finish-creating-your-connector"></a>手順 3.コネクタの作成を完了する

準備ができたら、**[作成]** を選んでコネクタをデプロイできるようにします。 既存のコネクタを更新する場合は、**[コネクタの更新]** を選びます。

お疲れさまでした。 ロジック アプリを作成すると、Logic Apps デザイナーでコネクタを検索し、ロジック アプリにそのコネクタを追加できるようになります。

![Logic Apps デザイナーでコネクタを見つける](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>他の Logic Apps ユーザーとコネクタを共有する

登録済みのカスタム コネクタは、認定を受けていなくても、Microsoft が管理するコネクタと同様に機能します。ただし、これらのコネクタを表示および使用できるのは、コネクタの作成者と、ロジック アプリがデプロイされているリージョンで同じ Azure Active Directory テナントとロジック アプリの Azure サブスクリプションを持つユーザーに "*限られます*"。 共有は任意ですが、コネクタを他のユーザーと共有することが望ましい場合があります。 

> [!IMPORTANT]
> コネクタを共有すると、他のユーザーがそのコネクタに依存し始める可能性があります。 
> ***コネクタを削除すると、そのコネクタへのすべての接続が削除されます。***
 
これらの境界の外側の外部ユーザー (たとえば、Logic Apps、Flow、PowerApps のすべてのユーザー) とコネクタを共有するには、[Microsoft の認定を受けるためにコネクタを提出](../logic-apps/custom-connector-submit-certification.md)します。

## <a name="faq"></a>FAQ

**Q:** カスタム コネクタには制限が何かありますか。 </br>
**A:** はい。カスタム コネクタの制限については[こちら](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits)をご覧ください。

## <a name="get-support"></a>サポートを受ける

* 開発とオンボーディングに関するサポートについて、または登録ウィザードにない機能の要求については、[condevhelp@microsoft.com](mailto:condevhelp@microsoft.com) にお問い合わせください。Microsoft では、開発者の質問や問題に対応するために、このアカウントを監視し、質問や問題を適切なチームに転送しています。

* [Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)では、質問の投稿や質問への回答を行うことができるほか、他の Azure Logic Apps ユーザーがどのようなことを行っているかがわかります。

* [Logic Apps ユーザー フィードバック サイト](http://aka.ms/logicapps-wish)でアイデアへの投票やアイデアの投稿を行って、Logic Apps の改善にご協力ください。 

## <a name="next-steps"></a>次の手順

* [省略可能: コネクタの認定を受ける](../logic-apps/custom-connector-submit-certification.md)
* [カスタム コネクタに関する FAQ](../logic-apps/custom-connector-faq.md)