---
title: Azure AD にサービス アプリを登録する - Azure API for FHIR
description: サービス クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 3eb942160e45c589377ee3ecd283ea2939a23934
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273402"
---
# <a name="register-a-service-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Azure API for FHIR の Azure Active Directory でサービス クライアント アプリケーションを登録する

この記事では、サービス クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。 クライアント アプリケーションの登録とは、トークンの認証および取得に使用できるアプリケーションの Azure Active Directory の表現です。 サービス クライアントは、対話型のユーザー認証なしでアクセス トークンを取得するために、アプリケーションから使用されることを目的としています。 アクセス トークンを取得するときには、特定のアプリケーション アクセス許可を持ち、アプリケーション シークレット (パスワード) を使用します。

新しいサービス クライアントを作成するには、以下の手順に従います。

## <a name="app-registrations-in-azure-portal"></a>Azure portal でのアプリの登録

1. [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]** に移動します。

2. **[アプリの登録]** を選択します。

    ![Azure portal。 新しいアプリの登録。](media/add-azure-active-directory/portal-aad-new-app-registration.png)

3. **[新規登録]** を選択します。

4. サービス クライアントに表示名を指定します。 サービス クライアント アプリケーションでは、一般に応答 URL を使用しません。

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure portal。新しいサービス クライアント アプリの登録。":::

5. **[登録]** を選択します。

## <a name="api-permissions"></a>API のアクセス許可

FHIR の Azure API のアクセス許可は、RBAC によって管理されます。 詳細については、「 [FHIR の AZURE RBAC の構成](configure-azure-rbac.md)」を参照してください。

>[!NOTE]
>Postman などのツールを使用して、FHIR 用の Azure API のアクセストークンをするときに、client_credentials の grant_type を使用します。 詳細については、「 [fhir の AZURE api での FHIR api のテスト](tutorial-web-app-test-postman.md)」を参照してください。

## <a name="application-secret"></a>アプリケーション シークレット

サービス クライアントでは、トークンを取得するためにシークレット (パスワード) を必要とします。

1. **[証明書とシークレット]** を選択します。
2. **[新しいクライアント シークレット]** を選択します。

    ![Azure portal。 サービス クライアント シークレット](media/add-azure-active-directory/portal-aad-register-new-app-registration-service-client-secret.png)

3. シークレットの説明と期間を指定します (1 年間、2 年間、または [なし])。

4. シークレットが生成されると、ポータルに一度だけ表示されます。 メモして、安全に保管してください。

## <a name="next-steps"></a>次のステップ

この記事では、サービス クライアント アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、Postman を使用して FHIR サーバーへのアクセスをテストします。
 
>[!div class="nextstepaction"]
>[Postman を使用して FHIR サービスにアクセスする](./../use-postman.md)
