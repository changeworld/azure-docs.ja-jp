---
title: Azure Active Directory B2C で組み込みポリシーを使用して Azure AD プロバイダーを追加する| Microsoft Docs
description: Open ID Connect の ID プロバイダー (Azure AD) を追加する方法を説明します。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 70d3a19b715052fe658102929a1c29cf3db2d595
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443731"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Azure Active Directory B2C: 組み込みポリシーを使用した Azure AD アカウントのサインイン

>[!NOTE]
> この機能はパブリック プレビュー段階にあります。 運用環境で、この機能を使用しないでください。

この記事では、組み込みポリシーを使用して、特定の Azure Active Directory (Azure AD) 組織のユーザーがサインインを行えるようにする方法について説明します。

## <a name="create-an-azure-ad-app"></a>Azure AD アプリの作成

特定の Azure AD 組織のユーザーのサインインを有効にするには、組織の Azure AD テナント内でアプリケーションを登録する必要があります。

>[!NOTE]
> 以下の手順では、組織の Azure AD テナントには "Contoso.com"を使用して、Azure AD B2C テナントとして "fabrikamb2c.onmicrosoft.com" を使用します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 上部のバーで、自分のアカウントを選択します。 **[ディレクトリ]** の一覧から、アプリケーションを登録する Azure AD テナント (contoso.com) を選択します。
1. 左側のウィンドウで **[All Services]**  を選択し、"App registrations" を検索します。
1. **[新しいアプリケーションの登録]** を選択します。
1. アプリケーションの名前を入力します (`Azure AD B2C App` など)。
1. アプリケーション タイプとして **[Web app / API] \(Web アプリ/API)** を選択します。
1. **[サインオン URL]** に次の URL を入力します。`yourtenant`は、Azure AD B2C テナントの名前 (`fabrikamb2c.onmicrosoft.com`) で置き換えられます。

    >[!NOTE]
    >**[サインイン URL]** の "yourtenant" の値は、すべて小文字にする必要があります。

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. アプリケーション ID を保存します｡この ID は次のセクションでクライアント ID として利用します｡
1. **[設定]** ブレードで **[キー]** を選択します。
1. **Passwords** のところで **Key description** を入力して､**Duration** を [Never expires] に設定します｡ 
1. **Save** をクリックして､生成されたキーの**値** を書き留めます｡このキー値は､次のセクションでクライアント シークレットとして利用します｡

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>テナントで Azure AD を ID プロバイダとして構成する

1. 上部のバーで、自分のアカウントを選択します。 **Directory** 一覧から Azure AD B2C テナント (fabrikamb2c.onmicrosoft.com) を選択します｡
1. Azure Portal で [Azure AD B2C settings メニューに移動します](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)｡
1. Azure AD B2C settings メニューで **Identity providers** をクリックします｡
1. ブレードの上部にある **[+追加]** をクリックします。
1. ID プロバイダー構成のわかりやすい **[名前]** を指定します。 たとえば､"Contoso Azure AD" を入力します｡
1. **[ID プロバイダーの種類]** をクリックし、**[Open ID Connect]** を選択して、**[OK]** をクリックします。
1. **[この ID プロバイダーをセットアップします]** をクリックします。
1. **Metadata url** には､次の URL を入力します。`yourtenant`は、Azure AD テナントの名前 (例: `contoso.com` ) で置き換えられます。

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. **Client ID** と **Client secret** には､前のセクションで取得したアプリケーション ID とキーをそれぞれ入力します｡
1. **Scope** は既定値 (`openid`) をそのまま残します｡
1. **Response type** は既定値 (`code`) をそのまま残します｡
1. **Response mode** は既定値 (`form_post`) をそのまま残します｡
1. 必要に応じて､**Domain** に値を入力します (例: `ContosoAD`)｡ この値は､要求で *domain_hint* を使用するこの ID プロバイダーを参照するときに使用します｡ 
1. Click **OK**.
1. **Map this identity provider's claims** をクリックします｡
1. **User ID** には `oid` を入力します｡
1. **Display Name** には `name` を入力します｡
1. **Given name** には `given_name` を入力します｡
1. **Surname** には `family_name` を入力します｡
1. **Email** には `unique_name` を入力します｡
1. **[OK]** をクリックし、**[作成]** をクリックして構成を保存します。

## <a name="next-steps"></a>次の手順

新しく作成した Azure AD ID プロバイダーを組み込みポリシーに追加して､[AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com) にフィードバックを提供する
