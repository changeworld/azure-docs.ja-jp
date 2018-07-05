---
title: Azure Active Directory B2C の Amazon の構成 | Microsoft Docs
description: Azure Active Directory B2C によってセキュリティ保護されたアプリケーションで、Amazon アカウントを使用するコンシューマーにサインアップとサインインを提供します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e3b3d66b913b595e68c03b68990d1a4806952579
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443704"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C: Amazon アカウントでコンシューマーにサインアップおよびサインインを提供する
## <a name="create-an-amazon-application"></a>Amazon アプリケーションを作成する
Azure Active Directory (Azure AD) B2C で ID プロバイダーとして Amazon を使用するには、Amazon アプリケーションを作成し、適切なパラメーターを提供する必要があります。 そのためには Amazon アカウントが必要です。 アカウントがない場合は、[http://www.amazon.com/](http://www.amazon.com/) で取得できます。

1. [Amazon Developer Center](https://login.amazon.com/) に移動し、Amazon アカウントの資格情報でサインインします。
2. まだ行っていない場合は、 **[Sign Up (サインアップ)]** をクリックして、開発者登録手順に従い、ポリシーを受け入れます。
3. **[Register new application]** をクリックします。
   
    ![Amazon Web サイトで新しいアプリケーションを登録する](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. アプリケーションの情報 (**[Name (名前)]**、**[Description (説明)]**、**[Privacy Notice URL (プライバシー規約 URL)]**) を提供し、**[Save (保存)]** をクリックします。
   
    ![Amazon で新しいアプリケーションを登録するためのアプリケーション情報を提供する](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. **[Web Settings (Web 設定)]** セクションで、**[Client ID (クライアント ID)]** および **[Client Secret (クライアント シークレット)]** の値をコピーします  (これを表示するには **[Show Secret (シークレットの表示)]** ボタンをクリックする必要があります)。テナントで ID プロバイダーとして Amazon を構成するには、両方の値が必要です。 セクションの下部にある **[Edit (編集)]** をクリックします。 **[Client Secret]** は、重要なセキュリティ資格情報です。
   
    ![Amazon で新しいアプリケーションのクライアント ID とクライアント シークレットを提供する](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. **[Allowed JavaScript Origins (許可された JavaScript オリジン)]** フィールドに「`https://login.microsoftonline.com`」と入力し、**[Allowed Return URLs (許可された 戻り先 URL)]** フィールドに「`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`」と入力します。 **{tenant}** は、実際のテナントの名前 (例: contoso.onmicrosoft.com) に置き換えます。 **[Save]** をクリックします。 **{tenant}** の値は大文字小文字が区別されます。
   
    ![Amazon で新しいアプリケーションの JavaScript 生成元と戻り先 URL を提供する](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>テナントで ID プロバイダーとして Amazon を構成する
1. この手順に従って、Azure Portal で [B2C 機能ブレードに移動](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) します。
2. B2C 機能ブレードで、 **[ID プロバイダー]** をクリックします。
3. ブレードの上部にある **[+追加]** をクリックします。
4. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば、「Amzn」などと入力します。
5. **[ID プロバイダーの種類]** をクリックし、**[Amazon]** を選択して、**[OK]** をクリックします。
6. **[この ID プロバイダーを設定する]** をクリックし、先に作成した Amazon アプリケーションのクライアント ID とクライアント シークレットを入力します。
7. **[OK]** をクリックし、**[作成]** をクリックして Amazon の構成を保存します。

