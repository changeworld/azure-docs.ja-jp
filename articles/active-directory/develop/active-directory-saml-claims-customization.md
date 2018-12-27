---
title: Azure AD のエンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ | Microsoft Docs
description: Azure AD のエンタープライズ アプリケーションの SAML トークンで発行された要求をカスタマイズする方法について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2018
ms.author: celested
ms.reviewer: luleon, jeedes
ms.custom: aaddev
ms.openlocfilehash: 4e80f5cb85a53281da9ec50a02d089f46e97dfde
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466718"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>方法: エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ

現在 Azure Active Directory (Azure AD) では、Azure AD アプリ ギャラリーの事前統合済みアプリケーション、カスタム アプリケーションを含め、ほとんどのエンタープライズ アプリケーションでシングル サインオンをサポートしています。 ユーザーが Azure AD によって SAML 2.0 プロトコルを使ってアプリケーションに対して認証されると、Azure AD は、アプリケーションにトークンを送信します (HTTP POST 経由)。 その後、アプリケーションがトークンを検証し、ユーザー名とパスワードの入力を求める代わりに、検証済みのトークンを使用してユーザーをログオンします。 これらの SAML トークンには、「要求」と呼ばれる、ユーザーに関する情報が含まれています。

*要求*とは、そのユーザーに発行するトークンの中にあるユーザーに関する ID プロバイダーが提示した情報を指します。 [SAML トークン](http://en.wikipedia.org/wiki/SAML_2.0)では、通常、このデータは SAML 属性ステートメントに含まれています。 ユーザーの一意の ID は通常、名前識別子とも呼ばれる SAML サブジェクトで表されます。

既定では、Azure AD は、アプリケーションに対して、Azure AD でのユーザーのユーザー名 (ユーザー プリンシパル名とも呼ばれます) を値に持つ NameIdentifier 要求を含む SAML トークンを発行します。 この値によって、ユーザーを一意に識別できます。 また、SAML トークンには、ユーザーの電子メール アドレス、姓名を含むその他の要求も含まれています。

アプリケーションに対して SAML トークンで発行された要求を表示または編集するには、Azure Portal でアプリケーションを開きます。 その後、アプリケーションの **[ユーザー属性]** セクションの **[その他のすべてのユーザー属性を表示および編集する]** チェック ボックスをオンにします。

![[ユーザー属性] セクション][1]

SAML トークンで発行された要求を編集する必要がある理由は、2 つ考えられます。
* アプリケーションが、別の要求 URI または要求値のセットを必要とするように記述されている。
* NameIdentifier 要求が、Azure AD に格納されているユーザー名 (ユーザー プリンシパル名) 以外の何かであることを必要とする方法で、アプリケーションがデプロイされている。

既定の要求値のすべてを編集できます。 SAML トークン属性のテーブルで要求の行を選びます。 **[属性の編集]** セクションが開き、要求の名前、値、および要求に関連付けられている名前空間を編集できます。

![ユーザー属性の編集][2]

コンテキスト メニューを使って、要求 (NameIdentifier 以外) を削除することもできます。メニューを開くには、**[...]** アイコンをクリックします。 **[属性の追加]** ボタンを使って、新しい要求を追加することもできます。

![ユーザー属性の編集][3]

## <a name="editing-the-nameidentifier-claim"></a>NameIdentifier 要求の編集

異なるユーザー名を使ってデプロイされたアプリケーションの問題を解決するには、**[ユーザー属性]** セクションの **[ユーザー識別子]** ドロップダウンを選択します。 この操作によって、複数のオプションがある次のダイアログが表示されます。

![ユーザー属性の編集][4]

### <a name="attributes"></a>属性

`NameIdentifier` (または NameID) 要求の必要なソースを選択します。 次のオプションから選択できます。

| Name | 説明 |
|------|-------------|
| 電子メール | ユーザーのメール アドレス |
| userprincipalName | ユーザーのユーザー プリンシパル名 (UPN) |
| onpremisessamaccount | オンプレミスの Azure AD から同期された SAM アカウント名 |
| objectID | Azure AD でのユーザーの objectID |
| EmployeeID | ユーザーの EmployeeID |
| ディレクトリ拡張機能 | [Azure AD Connect 同期を使用してオンプレミスの Active Directory から同期された](../hybrid/how-to-connect-sync-feature-directory-extensions.md)ディレクトリ拡張機能 |
| 拡張属性 1 ～ 15 | Azure AD のスキーマを拡張するために使用されるオンプレミスの 拡張機能属性 |

### <a name="transformations"></a>変換

特別な要求変換関数を使用することもできます。

| 関数 | 説明 |
|----------|-------------|
| **ExtractMailPrefix()** | メール アドレス、SAM アカウント名、またはユーザー プリンシパル名からドメイン サフィックスを除去します。 これにより、渡されたユーザー名の最初の部分のみが抽出されます (例: joe_smith@contoso.com ではなく "joe_smith" のみ)。 |
| **join()** | 属性を検証済みドメインと結合します。 選択したユーザー ID 値にドメインが含まれる場合、ユーザー名が抽出されて、選択された検証済みドメインが追加されます。 たとえば、ユーザー ID 値としてメール アドレス (joe_smith@contoso.com) を選択し、検証済みドメインとして contoso.onmicrosoft.com を選択した場合、結果は joe_smith@contoso.onmicrosoft.com になります。 |
| **ToLower()** | 選択した属性の文字を小文字に変換します。 |
| **ToUpper()** | 選択した属性の文字を大文字に変換します。 |

## <a name="adding-claims"></a>要求の追加

要求を追加する場合は、属性の名前を指定できます (SAML 仕様とは異なり、URI パターンに厳密に従う必要はありません)。 ユーザー属性には、ディレクトリに格納されている値を設定します。

![ユーザー属性の追加][7]

たとえば、ユーザーが所属している組織の部署 (営業部など) を要求として送信する必要があるとします。 アプリケーションで予期されているように要求名を入力し、値として **[user.department]** を選びます。

> [!NOTE]
> 特定のユーザーに対し、選択された属性に格納されている値がない場合、その要求はトークンで発行されません。

> [!TIP]
> **user.onpremisesecurityidentifier** と **user.onpremisesamaccountname** は、[Azure AD Connect ツール](../hybrid/whatis-hybrid-identity.md)を使ってオンプレミスの Active Directory のユーザー データを同期する場合にのみサポートされます。

## <a name="restricted-claims"></a>制限付き要求

SAML には制限付きの要求がいくつかあります。 これらの要求を追加すると、Azure AD は要求を送信しません。 SAML の制限付き要求は次のとおりです。

    | 要求の種類 (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>次の手順

* [Azure AD のアプリケーション管理](../manage-apps/what-is-application-management.md)
* [Azure AD アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンを構成する](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](howto-v1-debug-saml-sso-issues.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
