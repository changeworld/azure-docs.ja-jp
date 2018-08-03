---
title: LUIS のアカウント設定を管理する | Microsoft Docs
description: LUIS Web サイトを使用して、アカウント設定を管理します。
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/08/2018
ms.author: diberry
ms.openlocfilehash: 963a7f8c196702ea899ddfe31e6187a15eb5f683
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223210"
---
# <a name="manage-account-and-authoring-key"></a>アカウントとオーサリング キーを管理する
LUIS アカウントの 2 つの重要な情報は、ユーザー アカウントとオーサリング キーです。 ログイン情報は [account.microsoft.com](https://account.microsoft.com) で管理されます。 オーサリング キーは、[LUIS](luis-reference-regions.md) Web サイトの **[設定]** ページから管理されます。 

## <a name="authoring-key"></a>オーサリング キー

この (**[設定]** ページ上の) リージョン固有の 1 つのオーサリング キーを使用すると、[LUIS](luis-reference-regions.md) Web サイトだけでなく、[オーサリング API](https://aka.ms/luis-authoring-api) からすべてのアプリを作成できます。 必要に応じて、オーサリング キーは、各月のエンドポイントのクエリ数を[制限](luis-boundaries.md)できます。 

![LUIS の [設定] ページ](./media/luis-how-to-account-settings/account-settings.png)

オーサリング キーは、自分が所有しているすべてのアプリの他に、コラボレーターとして一覧に表示されるすべてのアプリで使用されます。

## <a name="authoring-key-regions"></a>オーサリング キーのリージョン
オーサリング キーは、[オーサリング リージョン](luis-reference-regions.md#publishing-regions)に固有です。 このキーは、別のリージョンでは機能しません。 

## <a name="reset-authoring-key"></a>オーサリング キーをリセットする
オーサリング キーが侵害された場合は、キーをリセットします。 [LUIS](luis-reference-regions.md) Web サイトのすべてのアプリでキーがリセットされます。 オーサリング API を使用してアプリを作成する場合は、`Ocp-Apim-Subscription-Key` の値を新しいキーに変更する必要があります。 

## <a name="delete-account"></a>アカウントを削除する
アカウントが削除されるときにどのようなデータが削除されるかについては、[データ ストレージと削除](luis-concept-data-storage.md#accounts)についての記事を参照してください。 

## <a name="azure-active-directory-tenant-user"></a>Azure Active Directory テナント ユーザー
LUIS では、Azure Active Directory (Azure AD) の標準的な同意フローが使用されます。 

テナント管理者は、Azure AD で LUIS を使用するためのアクセス許可を必要とするユーザーと直接的に協力する必要があります。 

まず、ユーザーが LUIS にサインインすると、管理者の承認を必要とするポップアップ ダイアログ ボックスが表示されます。 ユーザーは、続行する前にテナント管理者に問い合わせます。 

次に、テナント管理者が LUIS にサインインすると、同意フロー ポップアップ ダイアログが表示されます。 これは、管理者がユーザーにアクセス許可を与えるために必要なダイアログです。 管理者がアクセス許可を承認すると、ユーザーは LUIS の使用を続行できます。

テナント管理者は、LUIS にサインインしなくても、LUIS のための[同意](https://account.activedirectory.windowsazure.com/r#/applications)にアクセスできます。 

![アプリ Web サイトによる Azure Active Directory のアクセス許可](./media/luis-how-to-account-settings/tenant-permissions.png)

テナント管理者が特定のユーザーのみに LUIS の使用を許可する場合は、こちらの[ID ブログ](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/)を参照してください。

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>コラボレーター用の複数の電子メールを持つユーザー アカウント
LUIS アプリにコラボレーターを追加する場合は、コラボレーターがコラボレーターとして LUIS を使用するために必要な正確な電子メール アドレスを指定します。 Azure Active Directory (Azure AD) では、1 人のユーザーが複数の電子メール アカウントを交互に使用できますが、LUIS では、コラボレーターの一覧に指定された電子メール アドレスを使用してログインする必要があります。 


## <a name="next-steps"></a>次の手順

[オーサリング キー](luis-concept-keys.md#authoring-key)の詳細を確認します。 

