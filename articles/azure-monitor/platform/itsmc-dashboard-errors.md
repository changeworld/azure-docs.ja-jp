---
title: 一般的なエラー
description: このドキュメントでは、ダッシュボードに表示される一般的なエラーについて情報を提供します。
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: c3e6408a6a3473380edbe2966eba41970bd73562
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762762"
---
# <a name="errors-in-the-connector-status-section"></a>[コネクタの状態] セクションのエラー

ダッシュボードのコネクタの状態リスト セクションで、ITSM コネクタの問題を解決するのに役立つエラーを見つけることができます。

## <a name="status-common-errors"></a>状態の一般的なエラー

このセクションでは、[コネクタの状態] セクションに表示される一般的なエラーとその解決方法について説明します。

* **Error**: "Unexpected response from ServiceNow along with success status code. (成功状態コードと共に ServiceNow から予期しない応答が返されました。) Response: { "import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [ { "transform_map":"OMS Incident", "table": "incident", "status": "error", "error_message": "{Target record not found|Invalid table|Invalid staging table" }" (応答: { "import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "結果": [ { "transform_map":"OMS インシデント", "テーブル": "インシデント", "状態": "エラー", "error_message": "{ターゲット レコードが見つかりません|テーブルが無効です|ステージング テーブルが無効です" }")

    **原因**:このようなエラーは、次のような場合に ServiceNow から返されます。
  * ServiceNow インスタンスにデプロイされたカスタム スクリプトが原因で、インシデントが無視される。
  * "OMS インテグレーター アプリ" のコード自体が ServiceNow 側 (onBefore スクリプトなど) で変更された。

  **解決方法**:すべてのカスタム スクリプトまたはコードの変更を無効にします。

* **Error**: "{"error":{"message":"Operation Failed","detail":"ACL Exception Update Failed due to security constraints"}" ("{"エラー":{"メッセージ":"操作が失敗しました","詳細":"セキュリティ制約が原因で ACL 例外の更新が失敗しました"}")

    **原因**:ServiceNow のアクセス許可の構成に誤りがある。

    **解決方法**:すべてのロールが [指定](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)どおりに適切に割り当てられていることを確認します。

* **Error**: "An error occurred while sending the request. (この要求の送信中にエラーが発生しました。)"

    **原因**:"ServiceNow インスタンスが使用不可"

    **解決方法**:ServiceNow でインスタンスを確認してください。削除されているか、使用できない可能性があります。

* **Error**: "ServiceDeskHttpBadRequestException:StatusCode=429"

    **原因**:ServiceNow のレート制限が高すぎる、または低すぎる。

    **解決方法**:[こちら](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html)の説明に従い、ServiceNow インスタンスでレート制限を緩和するか、取り消してください。

* **Error**: "AccessToken and RefreshToken invalid. (AccessToken および RefreshToken が無効です。) User needs to authenticate again. (ユーザーはもう一度認証を受ける必要があります。)"

    **原因**:更新トークンの有効期限が切れている。

    **解決方法**:[こちら](./itsmc-resync-servicenow.md)の説明に従い、ITSM Connector を同期して新しい更新トークンを生成してください。

* **Error**: "Could not create/update work item for alert {alertName}. (アラート {alertName} の作業項目を作成または更新できませんでした。) ITSM Connector {connectionIdentifier} does not exist or was deleted. (ITSM Connector {connectionIdentifier} が存在しないか、削除されました。)"

    **原因**:ITSM Connector が削除された。

    **解決方法**:ITSM Connector が削除された一方で、それに関連付けられている ITSM アクション グループがまだ定義されています。 この問題を解決するには、2 つの方法があります。
  * 該当するアクション グループを見つけ、無効にするか削除する。
  * 既存の ITSM Connector を使用するように[アクション グループを再構成する](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。
  * [新しい ITSM Connector を作成し](./itsmc-definition.md#create-an-itsm-connection)、[それを使用するようにアクション グループを再構成する](itsmc-definition.md#create-itsm-work-items-from-azure-alerts)。

## <a name="ui-common-errors"></a>UI の一般的なエラー

* **エラー**:"Something went wrong. (問題が発生しました。) Could not get connection details. (接続の詳細を取得できませんでした。)" このエラーは、顧客が ITSM アクション グループを定義したときに表示されます。

    **原因**:新しく作成された ITSM Connector で、初期同期がまだ完了していない。

    **解決方法**:新しい ITSM Connector が作成されると、その ITSM Connector は、作業項目テンプレートや作業項目など、ITSM システムからの情報の同期を開始します。 [こちら](./itsmc-resync-servicenow.md)の説明に従い、ITSM Connector を同期して新しい更新トークンを生成してください。
