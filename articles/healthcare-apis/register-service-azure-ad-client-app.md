---
title: Azure Active Directory でサービス アプリケーションを登録する - Azure API for FHIR
description: この記事では、サービス アプリケーションを Azure Active Directory に登録する方法について説明します。
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 19f730c1d8a0fc0f809e8e16016795e725d80b61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60709895"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Azure Active Directory にサービス クライアント アプリケーションを登録する

この記事では、サービス クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。 クライアント アプリケーションの登録とは、トークンの認証および取得に使用できるアプリケーションの Azure Active Directory の表現です。 サービス クライアントは、対話型のユーザー認証なしでアクセス トークンを取得するために、アプリケーションから使用されることを目的としています。 アクセス トークンを取得するときには、特定のアプリケーション アクセス許可を持ち、アプリケーション シークレット (パスワード) を使用します。

以下の手順に従って新しいサービス クライアントを作成します。

## <a name="app-registrations-in-azure-portal"></a>Azure portal でのアプリの登録

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** をクリックします。

2. **[Azure Active Directory]** ブレードで、 **[アプリの登録 (プレビュー)]** をクリックします。

    ![Azure portal。 新しいアプリの登録。](media/how-to-aad/portal-aad-new-app-registration.png)

3. **[新規登録]** をクリックします。

## <a name="service-client-application-details"></a>サービス クライアント アプリケーションの詳細

* サービス クライアントには表示名が必要です。また、応答 URL を指定することもできますが、通常は使用されません。

    ![Azure portal。 新しいサービス クライアント アプリの登録。](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>API のアクセス許可

サービス クライアント アプリケーション ロールを付与する必要があります。 

1. **[API のアクセス許可]** を開き、[[FHIR API Resource Application Registration]\(FHIR API リソース アプリケーションの登録\)](register-resource-azure-ad-client-app.md) を選択します。

    ![Azure portal。 サービス クライアント API のアクセス許可](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. リソース アプリケーションで定義されているアプリケーション ロールから選択します。

    ![Azure portal。 サービス クライアント アプリケーションのアクセス許可](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. アプリケーションへの同意を許可します。 必要なアクセス許可を持っていない場合は、Azure Active Directory 管理者に確認してください。

    ![Azure portal。 サービス クライアント管理者の同意](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>アプリケーション シークレット

サービス クライアントにはシークレット (パスワード) が必要です。これはトークンを取得するときに使用されます。

1. **[証明書とシークレット]** をクリックします。

2. **[新しいクライアント シークレット]** をクリックします。

    ![Azure portal。 サービス クライアント シークレット](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. シークレットの期間を入力します。

4. 生成されると、ポータルに一度だけ表示されます。 メモして、安全に保管してください。

## <a name="next-steps"></a>次の手順

この記事では、サービス クライアント アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、Azure で FHIR API をデプロイします。
 
>[!div class="nextstepaction"]
>[オープン ソースの FHIR Serverをデプロイする](fhir-oss-powershell-quickstart.md)