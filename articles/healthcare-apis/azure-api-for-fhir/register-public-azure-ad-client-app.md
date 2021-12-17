---
title: Azure AD にパブリック クライアント アプリを登録する - Azure API for FHIR
description: この記事では、Azure での FHIR API のデプロイの準備として、パブリック クライアント アプリケーションを Azure Active Directory に登録する方法について説明します。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: e89d577385e41fc1e2dcd0b58afafc8ccf5371c8
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278312"
---
# <a name="register-a-public-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Azure API for FHIR の場合に Azure Active Directory でパブリック クライアント アプリケーションを登録する

この記事では、パブリック アプリケーションを Azure Active Directory に登録する方法について説明します。  

クライアント アプリケーションの登録とは、ユーザーに代わって認証を行い、API のアクセス許可を要求することができるアプリケーションの Azure Active Directory の表現です。 パブリック クライアントとは、シークレットの機密性を保持できないモバイル アプリケーションや単一ページの JavaScript アプリケーションなどのアプリケーションです。 この手順は [Confidential クライアントの登録](register-confidential-azure-ad-client-app.md)と似ていますが、パブリック クライアントがアプリケーションのシークレットを保持することは信頼できないため、追加する必要はありません。

このクイックスタートからは、[Microsoft ID プラットフォームにアプリケーションを登録する](../../active-directory/develop/quickstart-register-app.md)方法に関する全般情報が提供されます。

## <a name="app-registrations-in-azure-portal"></a>Azure portal でのアプリの登録

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** をクリックします。

2. **[Azure Active Directory]** ブレードで、 **[アプリの登録]** をクリックします。

    ![Azure portal。 新しいアプリの登録。](media/add-azure-active-directory/portal-aad-new-app-registration.png)

3. **[新規登録]** をクリックします。

## <a name="application-registration-overview"></a>アプリケーションの登録の概要

1. アプリケーションに表示名を与えます。

2. 応答 URL を入力してください。 応答 URL は、認証コードがクライアント アプリケーションに返される場所です。 後で応答 URL を追加し、既存のものを編集することができます。

    ![Azure portal。 新しいパブリック アプリの登録。](media/add-azure-active-directory/portal-aad-register-new-app-registration-pub-client-name.png)


[デスクトップ](../../active-directory/develop/scenario-desktop-app-registration.md)、[モバイル](../../active-directory/develop/scenario-mobile-app-registration.md)、[シングルページ](../../active-directory/develop/scenario-spa-app-registration.md) アプリケーションをパブリック アプリケーションとして構成するには:

1. [Azure portal](https://portal.azure.com) の **[アプリの登録]** でアプリを選択し、 **[認証]** を選択します。

2. **[詳細設定]** の **[既定のクライアントの種類]** を選択します。 **[アプリケーションは、パブリック クライアントとして扱います]** には **[はい]** を選択します。

3. シングルページ アプリケーションの場合、 **[アクセス トークン]** と **[ID トークン]** を選択して暗黙のフローを有効にします。

   - アプリケーションでユーザーがサインインする場合は、 **[ID トークン]** を選択します。
   - アプリケーションでも保護された Web API を呼び出す必要がある場合は、 **[アクセス トークン]** を選択します。

## <a name="api-permissions"></a>API のアクセス許可

FHIR の Azure API のアクセス許可は、RBAC によって管理されます。 詳細については、「 [FHIR の AZURE RBAC の構成](configure-azure-rbac.md)」を参照してください。

>[!NOTE]
>Postman などのツールを使用して、FHIR 用の Azure API のアクセストークンをするときに、client_credentials の grant_type を使用します。 詳細については、「 [fhir の AZURE api での FHIR api のテスト](tutorial-web-app-test-postman.md)」を参照してください。

## <a name="validate-fhir-server-authority"></a>FHIR サーバーのオーソリティを検証する
この記事で登録したアプリケーションと FHIR サーバーが同じ Azure AD テナントにある場合、次の手順に進むことができます。

クライアント アプリケーションを、FHIR サーバーとは別の Azure AD テナントで構成する場合は、**オーソリティ** を更新する必要があります。 Azure API for FHIR の [設定] --> [認証] でオーソリティを設定してください。 オーソリティを、``https://login.microsoftonline.com/\<TENANT-ID> ` に設定します。

## <a name="next-steps"></a>次のステップ

この記事では、パブリック クライアント アプリケーションを Azure Active Directory に登録する方法について学習しました。 次に、Postman を使用して FHIR サーバーへのアクセスをテストします。
 
>[!div class="nextstepaction"]
>[Postman を使用して FHIR サービスにアクセスする](./../use-postman.md)
