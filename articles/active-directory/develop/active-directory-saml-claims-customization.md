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
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e89a06de6232afef579c32d51137ddf577917436
ms.contentlocale: ja-jp
ms.lasthandoff: 04/27/2017


---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Azure Active Directory での事前に統合されたアプリの SAML トークンで発行された要求のカスタマイズ
現在 Azure Active Directory は、SAML 2.0 プロトコルを使用したシングル サインオンをサポートする 150 以上のアプリケーションを含め、何千もの事前統合済みのアプリケーションを Azure AD アプリケーション ギャラリーでサポートしています。 ユーザーが Azure AD によって SAML を使用してアプリケーションに対して認証されると、Azure AD は、アプリケーションにトークンを送信します (HTTP 302 リダイレクト経由)。 その後、アプリケーションがトークンを検証し、ユーザー名とパスワードの入力を求める代わりに、検証済みのトークンを使用してユーザーをログオンします。 これらの SAML トークンには、「要求」と呼ばれる、ユーザーに関する情報が含まれています。

ID 用語で「要求」とは、そのユーザーに発行するトークンの中にあるユーザーに関する ID プロバイダーが提示した情報を指します。 [SAML トークン](http://en.wikipedia.org/wiki/SAML_2.0)では、このデータは通常 SAML 属性ステートメントに含まれています。また、ユーザーの一意の ID は通常、SAML サブジェクトで表されます。

既定では、Azure AD は、アプリケーションに対して、Azure AD でのユーザー名の値を持つ NameIdentifier 要求を含む SAML トークンを発行します。 この値によって、ユーザーを一意に識別できます。 また、SAML トークンには、ユーザーの電子メール アドレス、姓名を含むその他の要求も含まれています。

アプリケーションに対して SAML トークンで発行された要求を表示または編集するには、Azure クラシック ポータルでアプリケーション レコードを開き、アプリケーションの下にある **[属性]** タブを選択します。

![[属性] タブ][1]

SAML トークンで発行された要求を編集する必要がある理由は、2 つ考えられます。
* アプリケーションが、別の要求 URI または要求値のセットを必要とするように記述されている。 
* NameIdentifier 要求が、Azure Active Directory に格納されているユーザー名 (ユーザー プリンシパル名) 以外の何かであることを必要とする方法で、アプリケーションがデプロイされている。 

既定の要求値のすべてを編集できます。 SAML トークンの属性テーブル内の行のいずれかにマウスを置いた際に、右側に表示される鉛筆の形のアイコンを選択します。 また、**[X]** アイコンを使用することで、(NameIdentifier 以外の) 要求を削除できます。さらに、**[ユーザー属性の追加]** ボタンを使用することで、新しい要求を追加できます。

## <a name="editing-the-nameidentifier-claim"></a>NameIdentifier 要求の編集
別のユーザー名を使用して、デプロイされたアプリケーションの問題を解決するには、NameIdentifier 要求の横にある鉛筆アイコンをクリックします。 この操作によって、複数のオプションがある次のダイアログが表示されます。

![ユーザー属性の編集][2]

**NameIdentifier 要求をディレクトリ内のユーザーの電子メール アドレスに設定する場合は、[属性値]** メニューの **[user.mail]** を選択します。 オンプレミスの Azure AD から同期されているユーザーの SAM アカウント名に設定する場合は、**[user.onpremisessamaccountname]** を選択します。

特殊な ExtractMailPrefix() 関数を使用して、電子メール アドレスまたはユーザー プリンシパル名からドメイン サフィックスを削除することもできます。 この場合、ユーザー名の最初の部分のみが渡されます (例: joe_smith@contoso.com ではなく "joe_smith" のみ)。

![ユーザー属性の編集][3]

## <a name="adding-claims"></a>要求の追加
要求を追加する場合は、属性の名前を指定できます (SAML 仕様とは異なり、URI パターンに厳密に従う必要はありません)。 ユーザー属性には、ディレクトリに格納されている値を設定します。

![ユーザー属性の追加][4]

たとえば、ユーザーが所属している組織の部署 (営業部など) を要求として送信する必要があるとします。 アプリケーションでどのような要求値が予期されているかを入力し、値として **[user.department]** を選択します。

特定のユーザーに対し、選択された属性に格納されている値がない場合、その要求はトークンで発行されません。

**注**: **user.onpremisesecurityidentifier** と **user.onpremisesamaccountname** は、[Azure AD Connect](../active-directory-aadconnect.md) ツールを使用してオンプレミスの Active Directory のユーザー データを同期する場合にのみサポートされます。

## <a name="next-steps"></a>次のステップ
* [Article Index for Application Management in Azure Active Directory](../active-directory-apps-index.md)
* [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](../active-directory-saas-custom-apps.md)
* [Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする方法](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ../media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ../media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ../media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ../media/active-directory-saml-claims-customization/claimscustomization4.png
