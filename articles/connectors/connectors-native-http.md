---
title: "HTTP 経由での任意のエンドポイントとの通信 - Azure Logic Apps |Microsoft Docs"
description: "HTTP 経由で任意のエンドポイントと通信できるロジック アプリを作成する"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan; LADocs
ms.openlocfilehash: 3eae7a4a47680fc36849fd413b76a80865cf3c9f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-http-action"></a>HTTP アクションの概要

HTTP アクションを使用すると、組織のワークフローを拡張し、HTTP 経由で任意のエンドポイントと通信することができます。

次のようにすることができます。

* 管理対象の Web サイトがダウンしたときにアクティブ化する (トリガーする) ロジック アプリ ワークフローを作成します。
* HTTP 経由で任意のエンドポイントと通信して、ワークフローを他のサービスに拡張します。

ロジック アプリで HTTP アクションの使用を開始する方法については、 [ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事をご覧ください。

## <a name="use-the-http-trigger"></a>HTTP トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 [トリガーの詳細についてはこちらを参照してください](connectors-overview.md)。

ロジック アプリ デザイナーで HTTP トリガーをセットアップする方法の例を次に示します。

1. ロジック アプリに HTTP トリガーを追加します。
2. ポーリングする HTTP エンドポイントのパラメーターを入力します。
3. ポーリングする頻度の繰り返し間隔を変更します。

   これで、ロジック アプリを起動すると各チェック時に内容が返されるようになりました。

   ![HTTP トリガー](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>HTTP トリガーのしくみ

HTTP トリガーは、定期的な間隔で HTTP エンドポイントを呼び出します。 既定では、HTTP 応答コードが 300 未満であった場合にロジック アプリが実行します。 ロジック アプリを起動するかどうかを指定するには、コード ビューでロジック アプリを編集して、HTTP 呼び出しの後に評価される条件を追加します。 返された状態コードが `400` 以上のときに起動する HTTP トリガーの例を次に示します。

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

HTTP トリガーのパラメーターの詳細については、 [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger)を参照してください。

## <a name="use-the-http-action"></a>HTTP アクションの使用

アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 
[アクションの詳細についてはこちらを参照してください](connectors-overview.md)。

1. **[新しいステップ]** > **[アクションの追加]** の順に選択します。
3. アクションの検索ボックスに「**http**」と入力して、HTTP アクションの一覧を表示します。
   
    ![Select the HTTP action](./media/connectors-native-http/using-action-1.png)

4. HTTP 呼び出しに必要なパラメーターを追加します。
   
    ![Complete the HTTP action](./media/connectors-native-http/using-action-2.png)

5. デザイナーのツールバーで、**[保存]** をクリックします。 ロジック アプリが保存され、同時に発行 (アクティブ化) されます。

## <a name="http-trigger"></a>HTTP トリガー
ここでは、このコネクタでサポートされているトリガーの詳細について説明します。 HTTP コネクタにはトリガーが 1 つあります。

| トリガー | [説明] |
| --- | --- |
| HTTP |HTTP 呼び出しを実行し、応答コンテンツを返します。 |

## <a name="http-action"></a>HTTP アクション
ここでは、このコネクタでサポートされているアクションの詳細について説明します。 HTTP コネクタには、使用可能なアクションが 1 つあります。

| アクションを表示します。 | [説明] |
| --- | --- |
| HTTP |HTTP 呼び出しを実行し、応答コンテンツを返します。 |

## <a name="http-details"></a>HTTP の詳細
次の表に、アクションの必須および省略可能な入力フィールドと、各アクションの使用に伴う出力の詳細を示します。

#### <a name="http-request"></a>HTTP 要求
HTTP 送信要求を実行するアクションの入力フィールドを次に示します。
\* は、必須フィールドであることを示しています。

| 表示名 | プロパティ名 | [説明] |
| --- | --- | --- |
| メソッド* |静的メソッド |使用する HTTP 動詞 |
| URI* |uri |HTTP 要求の URI |
| ヘッダー |headers |含める HTTP ヘッダーの JSON オブジェクト |
| 本文 |本文 |HTTP 要求の本文 |
| 認証 |[認証] |詳細については「 [認証](#authentication) 」セクションを参照 |

<br>

#### <a name="output-details"></a>出力の詳細
HTTP 応答の出力の詳細を次に示します。

| プロパティ名 | データ型 | [説明] |
| --- | --- | --- |
| headers |オブジェクト |応答ヘッダー |
| 本文 |オブジェクト |応答オブジェクト |
| 状態コード |int |HTTP 状態コード |

## <a name="authentication"></a>認証
Logic Apps 機能では、HTTP エンドポイントに対してさまざまな種類の認証を使用できます。 この認証は、**HTTP**、**[HTTP + Swagger](connectors-native-http-swagger.md)**、**[HTTP Webhook](connectors-native-webhook.md)** の各コネクタで使用できます。 次の種類の認証を構成できます。

* [基本認証](#basic-authentication)
* [クライアント証明書認証](#client-certificate-authentication)
* [Azure Active Directory (Azure AD) OAuth 認証](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>基本認証

基本認証には、次の認証オブジェクトが必要です。
\* は、必須フィールドであることを示しています。

| プロパティ名 | データ型 | [説明] |
| --- | --- | --- |
| Type* |型 |認証の種類 (基本認証の場合は `Basic` を指定する必要があります) |
| Username* |username |認証するユーザー名 |
| Password* |password |認証するパスワード。 |

> [!TIP]
> 定義から取得できないパスワードを使用する場合は、`securestring` パラメーターと `@parameters()` 
> [ ワークフロー定義関数](http://aka.ms/logicappdocs)を使用します。

例: 

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>クライアント証明書認証

クライアント証明書認証には、次の認証オブジェクトが必要です。 \* は、必須フィールドであることを示しています。

| プロパティ名 | データ型 | [説明] |
| --- | --- | --- |
| Type* |型 |認証の種類 (SSL クライアント証明書の場合は、 `ClientCertificate` を指定する必要があります) |
| PFX* |pfx |Base64 でエンコードされた Personal Information Exchange (PFX) ファイルのコンテンツ |
| Password* |password |PFX ファイルにアクセスするためのパスワード |

> [!TIP]
> ロジック アプリの保存後に定義内で読み取ることができなくなるパラメーターを使用するには、`securestring` パラメーターと `@parameters()` 
> [ ワークフロー定義関数](http://aka.ms/logicappdocs)を使用します。

例: 

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Azure AD OAuth 認証
Azure AD OAuth 認証には、次の認証オブジェクトが必要です。 \* は、必須フィールドであることを示しています。

| プロパティ名 | データ型 | [説明] |
| --- | --- | --- |
| Type* |型 |認証の種類 (Azure AD OAuth 認証の場合は `ActiveDirectoryOAuth` を指定する必要があります) |
| Tenant* |テナント |Azure AD テナントのテナント識別子。 |
| Audience* |対象となる読者 |使用許可を要求するリソース。 次に例を示します。`https://management.core.windows.net/` |
| Client ID* |clientId |Azure AD アプリケーションのクライアント識別子 |
| Secret* |secret |トークンを要求しているクライアントのシークレット |

> [!TIP]
> 保存後に定義内で読み取ることができなくなるパラメーターを使用するには、`securestring` パラメーターと `@parameters()` [ワークフロー定義関数](http://aka.ms/logicappdocs)を使用します。
> 
> 

例: 

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>次の手順
プラットフォームを試用し、 [ロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)してください。 [API リスト](apis-list.md)を参照すると、Logic Apps で使用可能な他のコネクタについて確認できます。

