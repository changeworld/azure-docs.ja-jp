---
title: "Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ | Microsoft Docs"
description: "Azure Active Directory において事前に統合されたアプリの SAML トークンで発行された要求をカスタマイズする方法を学習します"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: d3952e65aaf3ca89f83e99409e196840dd692a01
ms.lasthandoff: 04/12/2017


---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ
現在 Azure Active Directory は、SAML 2.0 プロトコルを使用したシングル サインオンをサポートする 150 以上のアプリケーションを含め、何千もの事前統合済みのアプリケーションを Azure AD アプリケーション ギャラリーでサポートしています。 SAML を使用し、Azure AD を介してアプリケーションを認証する場合、Azure AD は、(HTTP 302 リダイレクトを介して) アプリケーションにトークンを送信します。次に、アプリケーションはトークンを検証し、ユーザー名とパスワードを要求する代わりにトークンを使用してユーザーをログインさせます。 これらの SAML トークンには、「要求」と呼ばれる、ユーザーに関する情報が含まれています。

ID 用語で、「要求」とは、 ID プロバイダーが提示するユーザーに関する情報で、ユーザーに発行するトークンの中に収められています。 [SAML トークン](http://en.wikipedia.org/wiki/SAML_2.0)では、このデータは通常 SAML 属性ステートメントに含まれています。また、ユーザーの一意の ID は通常、SAML サブジェクトで表されます。

既定では、Azure AD はアプリケーションに対して SAML トークンを発行します。トークンには、NameIdentifier 要求と Azure AD でのユーザー名の値 (この値でユーザーを一意に識別します) が含まれます。 また、SAML トークンには、ユーザーの電子メール アドレス、姓名を含む、その他の要求も含まれています。

SAML トークンで発行された要求を表示し、編集するには、Microsoft Azure 管理ポータルでアプリケーション レコードを開き、アプリケーションの下にある **[属性]** タブを選択します。

![][1]

次の 2 つの理由から、SAML トークンで発行された要求を編集する必要が生じる場合があります。1 つは、アプリケーションが異なる要求 URL または要求値のセットを必要とするように作成されているためです。もう 1 つは、アプリケーションのデプロイには、Azure Active Directory に格納されているユーザー名 (ユーザー プリンシパル名ともいう) 以外の名前にする NameIdentifier 要求が必要になるためです。 

SAML トークンの属性テーブル内の行のいずれかにマウスを置いた際に、右側に表示される、鉛筆の形のアイコンを選択して、既定の要求値のいずれかを編集できます。 また、**[X]** アイコンを使用することで、(NameIdentifier 以外の) 要求を削除できます。さらに、**[ユーザー属性の追加]** ボタンを使用することで、新しい要求を追加できます。

## <a name="editing-the-nameidentifier-claim"></a>NameIdentifier 要求の編集
別のユーザー名を使用して、デプロイされたアプリケーションの問題を解決するには、NameIdentifier 要求の横にある鉛筆アイコンをクリックします。 これにより、次のいくつかのオプションを示すダイアログ ボックスが表示されます。

![][2]

**[属性値]** メニューで、**[user.mail]** を選択し、ディレクトリで NameIdentifier 要求がユーザーの電子メール アドレスになるように設定するか、**[user.onpremisessamaccountname]** を選択し、オンプレミスの Azure AD から同期されたユーザーのソフトウェア アセット管理アカウント名に設定します。 

また、特殊な ExtractMailPrefix() 関数を使用して、電子メール アドレスまたはユーザー プリンシパル名のいずれかからドメイン サフィックスを削除することもできます。これにより、ユーザー名の最初の部分のみが渡されます (例: joesmith@contoso.com の代わりに "joesmith" となります)。

![][3]

## <a name="adding-claims"></a>要求の追加
新しい要求を追加する場合は、値をディレクトリに格納されているユーザーの属性に設定することも、属性名 (SAML 仕様のように厳密に URI パターンに従う必要はありません) を指定することもできます。

![][4]

たとえば、組織でユーザーが所属する部門 (例: 営業) を要求として送信する必要がある場合、アプリケーションで求められる要求値を入力し、 **[user.department]** を値として選択できます。

特定のユーザーに対し、選択された属性に格納されている値がない場合は、トークンでその要求は発行されません。

**注**: **user.onpremisesecurityidentifier** と **user.onpremisesamaccountname** は、[Azure AD Connect](../active-directory-aadconnect.md) ツールを使用してオンプレミスの Active Directory のユーザー データを同期する場合にのみサポートされます。

## <a name="related-articles"></a>関連記事
* [Azure Active Directory のアプリケーション構成の管理に関する記事の索引](../active-directory-apps-index.md)
* [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](../active-directory-saas-custom-apps.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ../media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ../media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ../media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ../media/active-directory-saml-claims-customization/claimscustomization4.png
