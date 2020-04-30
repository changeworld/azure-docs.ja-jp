---
title: HTTP または HTTPS を使用してサービス エンドポイントを呼び出す
description: Azure Logic Apps からサービス エンドポイントに送信 HTTP または HTTPS 要求を送信します
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 9ed3d960b3f5653ea8706b39559c9d5a71c45a6c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867628"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Azure Logic Apps から HTTP または HTTPS でサービス エンドポイントを呼び出す

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) と組み込みの HTTP トリガーまたはアクションを使用すると、HTTP または HTTPS によって、サービス エンドポイントに要求を送信する自動化されたタスクやワークフローを作成できます。 たとえば Web サイトのサービス エンドポイントは、特定のスケジュールでそのエンドポイントを確認することによって監視が可能です。 Web サイトの停止など、そのエンドポイントで指定されたイベントが発生すると、そのイベントによってロジック アプリのワークフローがトリガーされ、そのワークフロー内のアクションが実行されます。 その代わりに、受信 HTTPS 呼び出しを受け取って応答する場合は、組み込みの [Request トリガーまたは Response アクション](../connectors/connectors-native-reqres.md)を使用します。

> [!NOTE]
> この HTTP コネクタは、ターゲット エンドポイントの機能に基づき、トランスポート層セキュリティ (TLS) バージョン 1.0、1.1、1.2 をサポートしています。 Logic Apps は、考えられる最高のサポート バージョンを使用してエンドポイントとネゴシエートします。 そのため、たとえばエンドポイントが 1.2 をサポートしている場合、コネクタはまず 1.2 を使用します。 それ以外の場合、コネクタは、2 番目に高いサポート バージョンを使用します。
>
> HTTP コネクタは、認証用の中間 TLS/SSL 証明書をサポートしていません。

定期的なスケジュールでエンドポイントを調べる、つまり "*ポーリング*" するには、ワークフローの最初のステップとして [HTTP トリガーを追加](#http-trigger)します。 トリガーによるエンドポイントの調査ごとに、トリガーからそのエンドポイントに対して、呼び出しまたは*要求*の送信が行われます。 エンドポイントの応答によって、ロジック アプリのワークフローが実行されるかどうかが決定します。 エンドポイントの応答からの任意のコンテンツが、トリガーによってロジック アプリのアクションに渡されます。

ワークフロー内のどこか他の場所からエンドポイントを呼び出すには、[HTTP アクションを追加します](#http-action)。 エンドポイントの応答によって、ワークフローの以降のアクションの実行方法が決まります。

> [!IMPORTANT]
> HTTP トリガーまたはアクションにこれらのヘッダーが含まれている場合、Logic Apps は警告やエラーを表示することなく、生成された要求メッセージからこれらのヘッダーを削除します。
>
> * `Accept-*`
> * `Allow`
> * `Content-*` (`Content-Disposition`、`Content-Encoding`、および `Content-Type` は例外です)
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Logic Apps では、これらのヘッダーが含まれる HTTP トリガーまたはアクションを使用するロジック アプリの保存は妨げられませんが、これらのヘッダーは無視されます。

この記事では、ロジック アプリのワークフローに HTTP トリガーまたはアクションを追加する方法について説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* 呼び出すターゲット エンドポイントの URL

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」を参照してください。

* ターゲット エンドポイントの呼び出し元となるロジック アプリ。 HTTP トリガーで開始するには、[空のロジック アプリを作成します](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 HTTP アクションを使用するには、任意のトリガーで対象のロジック アプリを起動します。 この例では、最初のステップとして HTTP トリガーを使用します。

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>HTTP トリガーの追加

この組み込みトリガーは、エンドポイントに指定された URL に対して HTTP 呼び出しを実行し、応答を返します。

1. [Azure portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーで空のロジック アプリを開きます。

1. デザイナーの検索ボックスで、 **[組み込み]** を選択します。 検索ボックスに、フィルターとして「`http`」と入力します。 **[トリガー]** の一覧から、 **[HTTP]** トリガーを選択します。

   ![HTTP トリガーを選択する](./media/connectors-native-http/select-http-trigger.png)

   この例では、トリガー名を "HTTP trigger" に変更して、このステップにわかりやすい名前を付けています。 また、この例では後で HTTP アクションを追加します。どちらの名前も一意である必要があります。

1. ターゲット エンドポイントへの呼び出しに含める [HTTP トリガー パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)の値を指定します。 トリガーでターゲット エンドポイントを確認する頻度を設定します。

   ![HTTP トリガー パラメーターを入力する](./media/connectors-native-http/http-trigger-parameters.png)

   **[なし]** 以外の認証の種類を選択した場合、認証設定は、選択に基づいて変化します。 HTTP に使用できる認証の種類の詳細については、以下のトピックを参照してください。

   * [送信呼び出しに認証を追加する](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [マネージド ID を利用してリソースへのアクセスを認証する](../logic-apps/create-managed-service-identity.md)

1. その他の使用可能なパラメーターを追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なパラメーターを選択します。

1. トリガーが起動したときに実行されるアクションを使用して、ロジック アプリのワークフローを引き続き構築します。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

<a name="http-action"></a>

## <a name="add-an-http-action"></a>HTTP アクションの追加

この組み込みアクションは、エンドポイントに指定された URL に対して HTTP 呼び出しを実行し、応答を返します。

1. [Azure portal](https://portal.azure.com) にサインインします。 ロジック アプリ デザイナーでロジック アプリを開きます。

   この例では、最初のステップとして HTTP トリガーを使用します。

1. HTTP アクションを追加するステップで、 **[新しいステップ]** を選択します。

   ステップの間にアクションを追加するには、ステップ間の矢印の上にポインターを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. **[アクションを選択してください]** で **[Built-in]\(組み込み\)** を選択します。 検索ボックスに、フィルターとして「`http`」と入力します。 **[アクション]** の一覧で、 **[HTTP]** アクションを選択します。

   ![HTTP アクションを選択する](./media/connectors-native-http/select-http-action.png)

   この例では、アクション名を "HTTP action" に変更して、このステップにわかりやすい名前を付けています。

1. ターゲット エンドポイントへの呼び出しに含める [HTTP アクション パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)の値を指定します。

   ![HTTP アクションのパラメーターを入力する](./media/connectors-native-http/http-action-parameters.png)

   **[なし]** 以外の認証の種類を選択した場合、認証設定は、選択に基づいて変化します。 HTTP に使用できる認証の種類の詳細については、以下のトピックを参照してください。

   * [送信呼び出しに認証を追加する](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [マネージド ID を利用してリソースへのアクセスを認証する](../logic-apps/create-managed-service-identity.md)

1. その他の使用可能なパラメーターを追加するには、 **[新しいパラメーターの追加]** リストを開き、必要なパラメーターを選択します。

1. 完了したら、忘れずに対象のロジック アプリを保存してください。 デザイナーのツール バーで、 **[保存]** を選択します。

## <a name="content-with-multipartform-data-type"></a>マルチパート/フォームデータ型のコンテンツ

HTTP 要求に `multipart/form-data` 型を含むコンテンツを処理する目的で、このフォーマットを使用することで、`$content-type` 属性と `$multipart` 属性を含む JSON オブジェクトを HTTP 要求の本文に追加できます。

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

たとえば、Excel ファイルの HTTP POST 要求を Web サイトに送信するロジック アプリがあるとします。このとき、`multipart/form-data` 型をサポートする、そのサイトの API を使用します。 このアクションは次のようになります。

![マルチパート フォーム データ](./media/connectors-native-http/http-action-multipart.png)

基礎ワークフロー定義で HTTP アクションの JSON 定義を示す同じ例は次のようになります。

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>コネクタのレファレンス

トリガーとアクションのパラメーターの詳細については、以下のセクションを参照してください。

* [HTTP トリガー パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP アクション パラメーター](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>出力の詳細

ここでは、以下の情報を返す HTTP トリガーまたはアクションからの出力の詳細情報を示します。

| プロパティ名 | Type | 説明 |
|---------------|------|-------------|
| headers | object | 要求のヘッダー |
| body | object | JSON オブジェクト | 要求の本文の内容を含むオブジェクト |
| status code | INT | 要求の状態コード |
|||

| status code | 説明 |
|-------------|-------------|
| 200 | [OK] |
| 202 | 承認済み |
| 400 | 正しくない要求 |
| 401 | 権限がありません |
| 403 | Forbidden |
| 404 | 見つかりません |
| 500 | 内部サーバー エラー。 不明なエラーが発生しました。 |
|||

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
