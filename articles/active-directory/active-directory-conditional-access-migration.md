---
title: "Azure Portal でクラシック ポリシーを移行する | Microsoft Docs"
description: "Azure Portal でクラシック ポリシーを移行します。"
services: active-directory
keywords: "アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c584eddb5542c2c49d08d35bcaf8e7acb5c5b83a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Azure Portal でクラシック ポリシーを移行する 


[条件付きアクセス](active-directory-conditional-access-azure-portal.md)は、Azure Active Directory (Azure AD) の 1 つの機能で、使用すると、承認されたユーザーがクラウド アプリにアクセスする方法を制御できます。 目的は変わりませんが、新しい Azure Portal のリリースにより、条件付きアクセスのしくみが大幅に改善されました。 Azure Portal 以外で構成した条件付きアクセス ポリシーは、Azure Portal で作成している新しいポリシーと共存できます。 それらは、無効にしたり削除したりしない限り、使用している環境で引き続き適用されます。 ただし、次の理由により、クラシック ポリシーを新しい Azure AD 条件付きアクセス ポリシーに移行することをお勧めします。

- 新しいポリシーを使用すると、クラシック ポリシーでは対処できなかったシナリオに対応できます。

- ポリシーを統合することで、管理する必要のあるポリシーの数を減らすことができます。   

このトピックは、既存のクラシック ポリシーを新しい Azure AD 条件付きアクセス ポリシーに移行する際に役立ちます。


## <a name="classic-policies"></a>クラシック ポリシー

Azure Portal で作成していない、Azure AD および Intune の条件付きアクセス ポリシーは、**クラシック ポリシー**とも呼ばれます。 クラシック ポリシーを移行するために、Azure クラシック ポータルにアクセスできる必要はありません。 Azure Portal には、クラシック ポリシーを確認できる [**[クラシック ポリシー (プレビュー)]** ビュー](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)が用意されています。

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>クラシック ポリシーを開く

**クラシック ポリシーを開くには:**

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. **[Azure Active Directory]** ページの **[管理]** セクションで、**[条件付きアクセス]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/02.png)
 
2. **[条件付きアクセス - ポリシー]** ページの **[管理]** セクションで、**[クラシック ポリシー (プレビュー)]** をクリックします。

3. クラシック ポリシーの一覧から、確認するポリシーを選択します。   

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Azure AD 条件付きアクセス ポリシー

このトピックでは、Azure AD 条件付きアクセス ポリシーの知識がなくてもクラシック ポリシーを移行できるようにする詳細な手順を紹介します。 ただし、Azure AD 条件付きアクセスの基本的な概念と用語を知っていると、移行エクスペリエンスの向上に役立ちます。

参照:

- 「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)」では、基本的な概念と用語について確認します。

- 「[Azure Active Directory での条件付きアクセスの基本](active-directory-conditional-access-azure-portal-get-started.md)」では、Azure Portal のユーザー インターフェイスについて理解を深めます。


 





## <a name="multi-factor-authentication-policy"></a>多要素認証ポリシー 

この例では、クラウドアプリに多要素認証** を要求するクラシック ポリシーを移行する方法を示します。 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**クラシック ポリシーを移行するには:**

1. [クラシック ポリシーを開き](#open-a-classic-policy)、構成設定を取得します。
2. 新しい Azure AD 条件付きアクセス ポリシーを作成し、クラシック ポリシーを置き換えます。 


### <a name="create-a-new-conditional-access-policy"></a>新しい条件付きアクセス ポリシーを作成する


1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. **[Azure Active Directory]** ページの **[管理]** セクションで、**[条件付きアクセス]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/02.png)



3. **[条件付きアクセス]** ページで **[新規]** ページを開くには、上部のツール バーの **[追加]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/03.png)

4. **[新規]** ページの **[名前]** ボックスに、ポリシーの名前を入力します。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/29.png)

5. **[割り当て]** セクションで、**[ユーザーとグループ]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/05.png)

    a. クラシック ポリシーですべてのユーザーを選択している場合は、**[すべてのユーザー]** をクリックします。 

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/35.png)

    b. クラシック ポリシーでグループを選択している場合は、**[ユーザーとグループの選択]** をクリックし、必要なユーザーとグループを選択します。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/36.png)

    c. 除外されたグループがある場合は、**[除外]** タブをクリックし、必要なユーザーとグループを選択します。 

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/37.png)

6. **[新規]** ページで **[クラウド アプリ]** ページを開くには、**[割り当て]** セクションで **[クラウド アプリ]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. **[クラウド アプリ]** ページで、次の手順を実行します。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/08.png)

    a. **[アプリを選択]** をクリックします。

    b. **[選択]**をクリックします。

    c. **[選択]** ページでクラウド アプリを選択し、**[選択]** をクリックします。

    d. **[クラウド アプリ]** ページで、**[完了]** をクリックします。



9. **[多要素認証を要求する]** を選択している場合:

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/26.png)

    a. **[アクセス制御]** セクションで、**[許可]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/27.png)

    b. **[許可]** ページで、**[アクセス権の付与]** をクリックし、**[多要素認証を要求する]** をクリックします。

    c. **[選択]**をクリックします。


10. **[オン]** をクリックしてポリシーを有効にします。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/30.png)

11. クラシック ポリシーを無効にします。 

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>次のステップ

- 条件付きアクセスポリシーの構成方法については、[Azure Active Directory での条件付きアクセスの使用](active-directory-conditional-access-azure-portal-get-started.md)に関する記事を参照してください。

- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](active-directory-conditional-access-best-practices.md)」を参照してください。 
