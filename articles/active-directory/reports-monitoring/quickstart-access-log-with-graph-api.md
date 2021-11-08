---
title: Microsoft Graph API を使用して Azure AD ログにアクセスする | Microsoft Docs
description: このクイックスタートでは、Graph API を使用してサインイン ログにアクセスする方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: report-monitor
ms.topic: quickstart
ms.date: 06/03/2021
ms.author: markvi
author: MarkusVi
manager: karenhoran
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bf12b1fd54ee8d3a7461418c2b8828070dfc926
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997307"
---
# <a name="quickstart-access-azure-ad-logs-with-the-microsoft-graph-api"></a>クイック スタート: Microsoft Azure AD API を使用して Azure AD ログにアクセスする 

Azure AD のサインイン ログの情報を使用すると、ユーザーのサインインが失敗した場合に何が起こったかを把握することができます。 このクイックスタートでは、Graph API を使用してサインイン ログにアクセスする方法を示します。


## <a name="prerequisites"></a>前提条件

このクイック スタートのシナリオを完了するための要件を次に示します。

- **Azure AD テナントへのアクセス** - Azure AD テナントにアクセスできない場合は、「[Azure の無料アカウントを今すぐ作成しましょう](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)」を参照してください。 
- **Isabella Simonsen というテスト アカウント** - テスト アカウントの作成方法がわからない場合は、「[クラウド ベースのユーザーを追加する](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)」を参照してください。


## <a name="perform-a-failed-sign-in"></a>失敗したサインインを実行する

この手順の目的は、Azure AD のサインイン ログに失敗したサインインの記録を作成することです。

**この手順を完了するには:**

1. 間違ったパスワードを使用して、Isabella Simonsen として [Azure portal](https://portal.azure.com/) にサインインします。

2. サインイン ログでサインインの記録を確認できるように、5 分間待ちます。 詳細については、「[アクティビティ レポート](reference-reports-latencies.md#activity-reports)」を参照してください。



## <a name="find-the-failed-sign-in"></a>失敗したサインインを見つける

このセクションでは、Graph API を使用してサインインに関する情報を取得する手順について説明します。

 ![Microsoft Graph Explorer クエリ](./media/quickstart-access-log-with-graph-api/graph-explorer-query.png)   

**失敗したサインインを確認するには:**

1. [Microsoft Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) に移動します。

2. グローバル管理者としてテナントにサインインします。

    ![Microsoft Graph Explorer 認証](./media/quickstart-access-log-with-graph-api/graph-explorer-authentication.png)   

3. **HTTP 動詞ドロップダウン リスト** で **[GET]** を選択します。

4. **API バージョン ドロップダウン リスト** で **[beta]** を選択します。

5. **クエリ要求アドレス バー** に「`https://graph.microsoft.com/beta/auditLogs/signIns?$top=100&$filter=userDisplayName eq 'Isabella Simonsen'`」を入力します。
 
6. **[クエリの実行]** をクリックします。

クエリの結果を確認します。

 ![Microsoft Graph Explorer 応答プレビュー](./media/quickstart-access-log-with-graph-api/response-preview.png)   


## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、テスト ユーザーを削除します。 Azure AD ユーザーの削除方法がわからない場合は、「[Azure AD からユーザーを削除する](../fundamentals/add-users-azure-active-directory.md#delete-a-user)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Active Directory レポートの概要](overview-reports.md)
