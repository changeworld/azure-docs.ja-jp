---
title: Azure AD に Confidential クライアント アプリを登録する - Azure API for FHIR
description: ユーザーの代わりに認証し、リソース アプリケーションへのアクセスを要求する Confidential クライアント アプリを Azure Active Directory に登録します。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 2851f230a19bfbafb514e9fb22fd3f37de1f610b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272549"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Azure API for FHIR の Azure Active Directory で Confidential クライアント アプリケーションを登録する

このチュートリアルでは、Confidential クライアント アプリケーションを Azure Active Directory (Azure AD) に登録する方法について説明します。  

クライアント アプリケーションの登録は、ユーザーに代わって認証に使用でき、[リソース アプリケーション](register-resource-azure-ad-client-app.md)へのアクセスを要求できるアプリケーションを Azure AD で表したものです。 Confidential クライアント アプリケーションは、信頼性が高く、シークレットを保持し、アクセス トークンの要求時にそのシークレットを提示できるアプリケーションです。 Confidential アプリケーションの例としては、サーバー側のアプリケーションがあります。 

新しい機密クライアント アプリケーションを登録するには、次の手順を参照してください。 

## <a name="register-a-new-application"></a>新しいアプリケーションの登録

1. [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。

1. **[アプリの登録]** を選択します。 

    :::image type="content" source="media/add-azure-active-directory/portal-aad-new-app-registration.png" alt-text="Azure portal。新しいアプリの登録。":::

1. **[新規登録]** を選択します。

1. アプリケーションにユーザー向け表示名を付与します。

1. **[サポートされているアカウントの種類]** では、アプリケーションを使用できる、または API にアクセスできるユーザーを選択します。

1. (オプション) **[リダイレクト URI]** を指定します。 これらの詳細は後で変更できますが、アプリケーションの応答 URL がわかっている場合は、ここで入力します。

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client.png" alt-text="新しい Confidential クライアント アプリの登録。":::

1. **[登録]** を選択します。

## <a name="api-permissions"></a>API のアクセス許可

FHIR の Azure API のアクセス許可は、RBAC によって管理されます。 詳細については、「 [FHIR の AZURE RBAC の構成](configure-azure-rbac.md)」を参照してください。

>[!NOTE]
>Postman などのツールを使用して、FHIR 用の Azure API のアクセストークンをするときに、client_credentials の grant_type を使用します。 詳細については、「 [fhir の AZURE api での FHIR api のテスト](tutorial-web-app-test-postman.md)」を参照してください。


## <a name="application-secret"></a>アプリケーション シークレット

1. **[Certificates & secrets]\(証明書とシークレット\)** を選択してから、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。 

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client-secret.png" alt-text="機密クライアント。アプリケーション シークレット。":::

1. クライアント シークレットの **[説明]** を入力します。 **[有効期限]** ドロップダウン メニューを選択して有効期限のタイム フレームを選択し、 **[追加]** をクリックします。

   :::image type="content" source="media/add-azure-active-directory/add-a-client-secret.png" alt-text="クライアント シークレットの追加。":::

1. クライアント シークレット文字列が作成された後、その **[値]** と **[ID]** をコピーし、選択した安全な場所に格納します。

   :::image type="content" source="media/add-azure-active-directory/client-secret-string-password.png" alt-text="クライアント シークレット文字列。"::: 

> [!NOTE]
>クライアント シークレット文字列は、Azure portal に 1 回だけ表示されます。 [Certificates & Secrets]\(クライアントとシークレット\) Web ページから移動し、そこに戻った場合、[値] 文字列はマスクされます。 クライアント シークレット文字列が生成された直後に、そのコピーを作成することが重要です。 クライアント シークレットのバックアップ コピーがない場合は、上記の手順を繰り返して再生成する必要があります。
 
## <a name="next-steps"></a>次の手順

この記事では、Azure AD に機密クライアント アプリケーションを登録する方法の手順について説明しました。 また、Azure Healthcare API に API アクセス許可を追加する方法の手順も説明しました。 最後に、アプリケーション シークレットを作成する方法を示しました。 さらに、Postman を使用して FHIR サーバーにアクセスする方法を学習できます。
 
>[!div class="nextstepaction"]
>[Postman を使用して FHIR サービスにアクセスする](./../use-postman.md)
