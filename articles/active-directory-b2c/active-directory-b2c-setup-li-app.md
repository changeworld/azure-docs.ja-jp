---
title: Azure Active Directory B2C の LinkedIn の構成 | Microsoft Docs
description: Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、LinkedIn アカウントを使用するコンシューマーにサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6db3832031a1bb960ee40c0e4fb8c3d0591a976c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34711700"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active Directory B2C: LinkedIn アカウントでコンシューマーにサインアップおよびサインインを提供する
## <a name="create-a-linkedin-application"></a>LinkedIn アプリケーションを作成する
Azure Active Directory (Azure AD) B2C で ID プロバイダーとして LinkedIn を使用するには、LinkedIn アプリケーションを作成し、適切なパラメーターを提供する必要があります。 そのためには LinkedIn アカウントが必要です。 アカウントがない場合は、[https://www.linkedin.com/](https://www.linkedin.com/) で取得できます。

1. [LinkedIn Developers](https://www.developer.linkedin.com/) の Web サイトに移動し、LinkedIn アカウントの資格情報でサインインします。
2. 上部メニュー バーの **[My Apps (マイ アプリ)]** をクリックし、**[Create Application (アプリケーションの作成)]** をクリックします。
   
    ![LinkedIn - 新しいアプリ](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. **[Create a New Application (新しいアプリケーションの作成)]** フォームで、関連する情報 (**[Company Name (会社名)]**、**[Name (名)]**、**[Description (説明)]**、**[Application Logo URL (アプリケーション ロゴ URL)]**、**[Application Use (アプリケーションの用途)]**、**[Website URL (Web サイト URL)]**、**[Business Email (ビジネス メール)]**、**[Business Phone (ビジネス フォン)]**) を入力します。
4. **LinkedIn API の使用条件**に同意し、**[Submit (送信)]** をクリックします。
   
    ![LinkedIn - アプリの登録](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. **[クライアント ID]** と **[クライアント シークレット]** の値をコピーします。 (**[Authentication Keys (認証キー)]** セクションの下にあります)。テナントで ID プロバイダーとして LinkedIn を構成するには、両方の値が必要です。
   
   > [!NOTE]
   > **[Client Secret]** は、重要なセキュリティ資格情報です。
   > 
   > 
6. **[OAuth 2.0]** セクションの **[Authorized Redirect URLs (承認済みのリダイレクト URL)]** フィールドに「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contoso.onmicrosoft.com) に置き換えます。 **[Add (追加)]** をクリックし、**[Update (更新)]** をクリックします。 **{tenant}** 値は小文字にする必要があります。
   
    ![LinkedIn - アプリの設定](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして LinkedIn を構成する
1. この手順に従って、Azure ポータルで [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) します。
2. B2C 機能ブレードで、 **[ID プロバイダー]** をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「LI」などと入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[LinkedIn]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーを設定する]** をクリックし、先に作成した LinkedIn アプリケーションのクライアント ID とクライアント シークレットを入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして LinkedIn の構成を保存します。

