---
title: Azure AD にサービス アプリを登録する - Azure API for FHIR
description: サービス クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: cavoeg
ms.openlocfilehash: 84e767c91f2a48940aeed501bcec7e6739fd6772
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397114"
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

これでアプリケーションが登録されたので、ユーザーに代わり、このアプリケーションによって要求できる API アクセス許可を選択する必要があります。

1. **[API のアクセス許可]** を選択します。
1. **[アクセス許可の追加]** を選択します。

    Azure API for FHIR を使用する場合は、 **[所属する組織で使用している API]** で **Azure Healthcare API** を検索し、Azure Healthcare API へのアクセス許可を追加します。 

    別のリソース アプリケーションを参照している場合は、 **[自分の API][ で作成済みの](register-resource-azure-ad-client-app.md)FHIR API リソース アプリケーションの登録** を選択します。

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="Confidential クライアント。自分の組織の API" lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. ユーザーに代わって Confidential アプリケーションが要求できるスコープ (アクセス許可) を選択します。

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="サービス クライアント。委任されたアクセス許可":::

1. アプリケーションへの同意を許可します。 必要なアクセス許可を持っていない場合は、Azure Active Directory 管理者に確認してください。

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="サービス クライアント。同意を付与する":::

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
>[Postman を使用して FHIR サービスにアクセスする](../fhir/using-postman.md)
