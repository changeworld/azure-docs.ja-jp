---
title: Azure AD に Confidential クライアント アプリを登録する - Azure API for FHIR
description: ユーザーの代わりに認証し、リソース アプリケーションへのアクセスを要求する Confidential クライアント アプリを Azure Active Directory に登録します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: matjazl
ms.openlocfilehash: c10b27d375e2bfb8c64130eceb416a633241cf68
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284437"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Azure Active Directory に Confidential クライアント アプリケーションを登録する

このチュートリアルでは、Azure Active Directory (Azure AD) に機密クライアントアプリケーションを登録する方法について説明します。  

クライアントアプリケーションの登録は、ユーザーに代わって認証を行い、 [リソースアプリケーション](register-resource-azure-ad-client-app.md)へのアクセスを要求するために使用できるアプリケーションの Azure AD 表現です。 Confidential クライアント アプリケーションは、信頼性が高く、シークレットを保持し、アクセス トークンの要求時にそのシークレットを提示できるアプリケーションです。 Confidential アプリケーションの例としては、サーバー側のアプリケーションがあります。 

新しい confidential クライアントアプリケーションを登録するには、次の手順を参照してください。 

## <a name="register-a-new-application"></a>新しいアプリケーションの登録

1. [Azure portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。

1. **[アプリの登録]** を選択します。 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="Azure portal。新しいアプリの登録。":::

1. **[新規登録]** を選択します。

1. アプリケーションにユーザー向けの表示名を指定します。

1. **サポートされているアカウントの種類** については、アプリケーションを使用できるユーザーまたは API にアクセスできるユーザーを選択します。

1. Optional **リダイレクト URI** を指定します。 これらの詳細は後で変更できますが、アプリケーションの応答 URL がわかっている場合は、ここで入力します。

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="新しい Confidential クライアント アプリの登録。":::

1. **[登録]** を選択します。

## <a name="api-permissions"></a>API のアクセス許可

アプリケーションの登録が完了したので、このアプリケーションがユーザーに代わって要求する API アクセス許可を選択する必要があります。

1. **[API のアクセス許可]** を選択します。

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="Confidential クライアント。API のアクセス許可。":::

1. **[アクセス許可の追加]** を選択します。

    Azure API for FHIR を使用している場合は、「**組織が使用** している api」で **AZURE の医療 api** を検索することで、azure の医療 api にアクセス許可を追加します。 Azure の医療 API の検索結果は、 [FHIR 用の AZURE API](fhir-paas-powershell-quickstart.md)を既にデプロイしている場合にのみ返されます。

    別のリソースアプリケーションを参照している場合は、先ほど **api** で作成した [FHIR API リソースアプリケーションの登録](register-resource-azure-ad-client-app.md)を選択します。


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Confidential クライアント。自分の組織の API" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. ユーザーの代わりに、confidential クライアントアプリケーションが要求するスコープ (アクセス許可) を選択します。 **user_impersonation** を選択し、 **[アクセス許可の追加]** を選択します。

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Confidential クライアント。委任されたアクセス許可":::


## <a name="application-secret"></a>アプリケーション シークレット

1. **[Certificates & secrets]\(証明書とシークレット\)** を選択してから、 **[New client secret]\(新しいクライアント シークレット\)** を選択します。 

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="Confidential クライアント。アプリケーションシークレット。":::

1. クライアント シークレットの **[説明]** を入力します。 [有効 **期限** ] ドロップダウンメニューを選択して有効期限のタイムフレームを選択し、[ **追加**] をクリックします。

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="クライアントシークレットを追加します。":::

1. クライアントシークレット文字列を作成したら、その **値** と **ID** をコピーし、任意の安全な場所に保存します。

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="クライアントシークレット文字列。"::: 

> [!NOTE]
>クライアントシークレット文字列は、Azure portal で1回だけ表示されます。 [証明書 & シークレット] web ページから移動してから、それに戻ると、値の文字列がマスクされます。 生成された直後にクライアントシークレット文字列をコピーすることが重要です。 クライアントシークレットのバックアップコピーがない場合は、上記の手順を繰り返して再生成する必要があります。
 
## <a name="next-steps"></a>次のステップ

この記事では、Azure AD に機密クライアントアプリケーションを登録する手順について説明しました。 また、API のアクセス許可を Azure の医療 API に追加する手順についても説明しました。 最後に、アプリケーションシークレットを作成する方法を示しました。 さらに、Postman を使用して FHIR サーバーにアクセスする方法についても説明します。
 
>[!div class="nextstepaction"]
>[Postman を使用して Azure API for FHIR にアクセスする](access-fhir-postman-tutorial.md)
