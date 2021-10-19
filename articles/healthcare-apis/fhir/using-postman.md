---
title: Postman を使用して Azure の医療 Api FHIR サービスにアクセスする
description: この記事では、Postman を使用して Azure の医療 Api FHIR サービスにアクセスする方法について説明します。
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: ginle
ms.openlocfilehash: 908e9a9c17f93725b3cfb7a464224539379fddd1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777601"
---
# <a name="access-the-fhir-service-using-postman"></a>Postman を使用して FHIR サービスにアクセスする

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、 [Postman](https://www.getpostman.com/)を使用して、医療 API fhir サービス (fhir サービスと呼ばれる) にアクセスする手順について説明します。

## <a name="prerequisites"></a>前提条件

* Azure にデプロイされた FHIR サービス。 FHIR サービスをデプロイする方法の詳細については、「 [deploy a FHIR service](fhir-portal-quickstart.md)」を参照してください。
* FHIR サービスにアクセスするための登録されたクライアントアプリケーション。 クライアントアプリケーションを登録する方法の詳細については、「 [Azure Active Directory でのサービスクライアントアプリケーションの登録](../register-application.md)」を参照してください。 
* FHIR サービスにアクセスするために、クライアントアプリケーションとユーザーアカウントに付与されたアクセス許可 ("FHIR データ共同作成者" など)。 詳細については、「 [Azure の医療 api での FHIR サービスのデプロイ](fhir-portal-quickstart.md)」を参照してください。
* Postman はローカルにインストールされています。 Postman の詳細については、「[はじめに with Postman](https://www.getpostman.com/)」を参照してください。

## <a name="using-postman-create-workspace-collection-and-environment"></a>Postman の使用: ワークスペース、コレクション、および環境の作成

Postman を初めて使用する場合は、次の手順に従います。 それ以外の場合は、この手順はスキップできます
 
Postman では、ワークスペースの概念が導入され、ユーザーとチームが Api、コレクション、環境、およびその他のコンポーネントを共有できるようになりました。 既定の "マイワークスペース" または "チームワークスペース" を使用したり、新しいワークスペースを作成したりすることができます。
 
[![Postman で新しいワークスペースを作成します。 ](media/postman/postman-create-new-workspace.png) ](media/postman/postman-create-new-workspace.png#lightbox)

次に、関連するすべての REST API 要求をグループ化できる新しいコレクションを作成します。 ワークスペースで、[ **コレクションの作成**] を選択します。 既定の名前をそのまま使用する **ことも、** 名前を変更することもできます。 変更は自動的に保存されます。

[![新しいコレクションを作成します。 ](media/postman/postman-create-a-new-collection.png) ](media/postman/postman-create-a-new-collection.png#lightbox)

Postman コレクションをインポートおよびエクスポートすることもできます。 詳細については、 [Postman のドキュメント](https://learning.postman.com/docs/getting-started/importing-and-exporting-data/)を参照してください。

[![データをインポートします。 ](media/postman/postman-import-data.png) ](media/postman/postman-import-data.png#lightbox)

## <a name="create-or-update-environment-variables"></a>環境変数を作成または更新します。

要求で完全な url を使用できますが、url とその他のデータを変数に格納し、それらを使用することをお勧めします。

FHIR サービスにアクセスするには、次の変数を作成または更新する必要があります。

* **tenantid** – FHIR サービスがデプロイされている Azure テナント。 これは、[ **アプリケーション登録の概要** ] メニューオプションから検索できます。
* **subid** – FHIR サービスがデプロイされている Azure サブスクリプション。 これは、 **Fhir サービス** の [概要] メニューオプションにあります。
* **clientid** –アプリケーションクライアントの登録 ID。
* **clientsecret** –アプリケーションクライアント登録シークレット。
* **fhirurl** – FHIR サービスの完全な url です。 たとえば、「 `https://xxx.azurehealthcareapis.com` 」のように入力します。 これは、 **Fhir サービス** の [概要] メニューオプションにあります。
* **bearerToken** –スクリプトに Azure Active Directory (Azure AD) アクセストークンを格納する変数です。 空白のままにします。

> [!NOTE]
> クライアントアプリケーションの登録で、リダイレクト URL を構成していることを確認し [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) ます。

[![環境変数。 ](media/postman/postman-environments-variable.png) ](media/postman/postman-environments-variable.png#lightbox)

## <a name="connect-to-the-fhir-server"></a>fhir サーバーへの Connect

Postman を開き、使用する **ワークスペース**、 **コレクション**、および **環境** を選択します。 アイコンを選択して、 `+` 新しい要求を作成します。 

[![新しい要求を作成します。 ](media/postman/postman-create-new-request.png) ](media/postman/postman-create-new-request.png#lightbox)

## <a name="get-capability-statement"></a>Get 機能ステートメント

`{{fhirurl}}/metadata`要求に「」と入力し、 `GET` ヒット `Send` します。 FHIR サービスの機能ステートメントが表示されます。

[![機能ステートメントのパラメーター。 ](media/postman/postman-capability-statement.png) ](media/postman/postman-capability-statement.png#lightbox)

[![要求を保存します。 ](media/postman/postman-save-request.png) ](media/postman/postman-save-request.png#lightbox)

## <a name="get-azure-ad-access-token"></a>Azure AD アクセストークンの取得

FHIR サービスは Azure AD によって保護されています。 既定の認証を無効にすることはできません。 fhir サービスにアクセスするには、最初に Azure AD アクセストークンを取得する必要があります。 詳細については、「 [Microsoft ID プラットフォームアクセストークン](../../active-directory/develop/access-tokens.md)」を参照してください。

新しい要求を作成し `POST` ます。

1. 要求ヘッダーに「」と入力します。 `https://login.microsoftonline.com/{{tenantid}}/oauth2/token`

2. [ **本文** ] タブを選択し、[ **url エンコード**] を選択します。 [キーと値] セクションで、次の値を入力します。
    - **grant_type**: `Client_Credentials`
    - **client_id**: `{{clientid}}`
    - **client_secret**: `{{clientsecret}}`
    - **リソース**: `{{fhirurl}}`
    
3. [ **テスト** ] タブを選択し、テキストセクションに「」と入力します。 `pm.environment.set("bearerToken", pm.response.json().access_token);`
4. **[保存]** を選択して設定を保存します。
5. **Send** を押します。 Azure AD アクセストークンを含む応答が表示されます。これは、変数に `accessToken` 自動的に保存されます。 これは、すべての FHIR サービス API 要求で使用できます。

  [![[送信] ボタン。 ](media/postman/postman-send-button.png) ](media/postman/postman-send-button.png#lightbox)

などのオンラインツールを使用してアクセストークンを調べることができ [https://jwt.ms](https://jwt.ms) ます。 [ **要求** ] タブを選択すると、トークン内の各要求に関する詳細な説明が表示されます。

[![アクセストークン要求。 ](media/postman/postman-access-token-claims.png) ](media/postman/postman-access-token-claims.png#lightbox)

## <a name="get-fhir-resource"></a>FHIR リソースの取得

Azure AD アクセストークンを取得した後、fhir データにアクセスできます。 新しい要求で `GET` 、「」と入力し `{{fhirurl}}/Patient` ます。

認証の種類として [ **ベアラートークン** ] を選択します。  [ `{{bearerToken}}` **トークン** ] セクションに「」と入力します。 **[Send]** を選択します。 応答として、FHIR リソースの患者の一覧が表示されます。

[![[ベアラートークン] を選択します。 ](media/postman/postman-select-bearer-token.png) ](media/postman/postman-select-bearer-token.png#lightbox)

## <a name="create-or-update-your-fhir-resource"></a>FHIR リソースを作成または更新します。

Azure AD アクセストークンを取得した後、fhir データを作成または更新できます。 たとえば、新しい患者を作成したり、既存の患者を更新したりすることができます。
 
新しい要求を作成し、メソッドを "Post" に変更して、要求セクションに値を入力します。

`{{fhirurl}}/Patient`

認証の種類として [ **ベアラートークン** ] を選択します。  [ `{{bearerToken}}` **トークン** ] セクションに「」と入力します。 [ **本文** ] タブを選択します。 **Raw** オプションと **JSON** を本文テキスト形式で選択します。 本文セクションにテキストをコピーして貼り付けます。 


```
{
    "resourceType": "Patient",
    "active": true,
    "name": [
        {
            "use": "official",
            "family": "Kirk",
            "given": [
                "James",
                "Tiberious"
            ]
        },
        {
            "use": "usual",
            "given": [
                "Jim"
            ]
        }
    ],
    "gender": "male",
    "birthDate": "1960-12-25"
}
```
**[Send]** を選択します。 JSON 応答に新しい患者が表示されます。

[![[送信] ボタンをクリックして、新しい患者を作成します。 ](media/postman/postman-send-create-new-patient.png) ](media/postman/postman-send-create-new-patient.png#lightbox)

## <a name="export-fhir-data"></a>FHIR データをエクスポートする

Azure AD アクセストークンを取得した後、fhir データを Azure ストレージアカウントにエクスポートできます。

新しい要求を作成し `GET` ます。 `{{fhirurl}}/$export?_container=export`

認証の種類として [ **ベアラートークン** ] を選択します。  [ `{{bearerToken}}` **トークン** ] セクションに「」と入力します。 **ヘッダー** を選択して、2つの新しいヘッダーを追加します。

- **同意**: `application/fhir+json`
- **優先**:  `respond-async`

**Send** を押します。 応答があることを確認してください `202 Accepted` 。 応答の [ **ヘッダー** ] タブを選択し、 **コンテンツの場所** の値をメモしておきます。 値を使用して、エクスポートジョブの状態を照会できます。

[![投稿して、新しい患者202の受け入れられた応答を作成します。 ](media/postman/postman-202-accepted-response.png) ](media/postman/postman-202-accepted-response.png#lightbox)

## <a name="next-steps"></a>次の手順

この記事では、Postman を使用して Azure の医療 Api で FHIR サービスにアクセスする方法について説明しました。 Azure の医療 Api における FHIR サービスの詳細については、「」を参照してください。

>[!div class="nextstepaction"]
>[FHIR サービスとは](overview.md)