---
title: "Azure Active Directory でカスタム ドメインにユーザーを割り当てる | Microsoft Docs"
description: "Azure Active Directory のカスタム ドメインにユーザー アカウントを設定する方法です。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 717b5a7c-7bc3-4ab1-98b5-4740b53338fe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 39cb54a6637088c35c6aef864a804c24803f48ba
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---
# <a name="assign-users-to-a-custom-domain"></a>カスタム ドメインにユーザーを割り当てる
カスタム ドメインを Azure Active Directory に追加した後は、このドメインのユーザー アカウントを追加する必要があります。その後ユーザー アカウントの認証を開始できます。

> [!IMPORTANT]
> Microsoft では、この記事で言及している Azure クラシック ポータルを使用するのではなく、Azure Portal の [Azure AD 管理センター](https://aad.portal.azure.com)を使用して Azure AD を管理することをお勧めしています。 Azure AD 管理センターでドメイン名を管理する方法については、「[Azure Active Directory のカスタム ドメイン名の管理](active-directory-domains-manage-azure-portal.md)」を参照してください。

## <a name="users-synced-from-a-on-premises-directory"></a>オンプレミスのディレクトリから同期されたユーザー
既にオンプレミスの Active Directory と Azure Active Directory の間の接続を設定してある場合は、同期することで、アカウントを設定できます。 オンプレミスの Active Directory と Azure Active Directory を同期する方法の詳細については、「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。

## <a name="users-added-and-managed-in-the-cloud"></a>クラウドで追加され管理されるユーザー
既存のユーザー アカウントのドメインを変更するには次の手順を実行します。

1. グローバル管理者またはユーザー管理者であるアカウントを使用して Azure クラシック ポータルを開きます。
2. ディレクトリを開きます。
3. **[ユーザー]** タブをクリックします。
4. 一覧からユーザーを選択します。
5. ユーザーのドメインを変更し、 **[保存]**を選択します。

この操作は、[Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) または [Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) を使用しても実行できます。

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>新しいユーザーを作成するときにカスタム ドメインを選択する
1. グローバル管理者またはユーザー管理者であるアカウントを使用して Azure クラシック ポータルを開きます。
2. ディレクトリを開きます。
3. **[ユーザー]** タブをクリックします。
4. コマンド バーで、 **[追加]**を選択します。
5. ユーザー名を追加するときに、ドメインの一覧からカスタム ドメインを選択します。
6. [ **保存**] を選択します。

## <a name="next-steps"></a>次のステップ
* [カスタム ドメイン名を使用してユーザーのサインインを省力化する](active-directory-add-domain.md)
* [カスタム ドメイン名を管理する](active-directory-add-manage-domain-names.md)
* [Azure AD のドメイン管理の概念を理解する](active-directory-add-domain-concepts.md)


