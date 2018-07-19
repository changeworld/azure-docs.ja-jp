---
title: Azure portal での多要素認証が必要なクラシック ポリシーの移行 | Microsoft Docs
description: この記事では、Azure portal で多要素認証が必要なクラシック ポリシーを移行する方法を示します。
services: active-directory
keywords: アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 6190a8ee90855223779751373bf16ca3db0fe761
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37871504"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Azure portal での多要素認証が必要なクラシック ポリシーの移行 

この記事では、クラウド アプリの**多要素認証**が必要なクラシック ポリシーを移行する方法を示します。 前提条件ではありませんが、クラシック ポリシーの移行を開始する前に「[Azure Portal でクラシック ポリシーを移行する](active-directory-conditional-access-migration.md)」を読むことをお勧めします。


 
## <a name="overview"></a>概要 

この記事のシナリオでは、クラウド アプリの**多要素認証**が必要なクラシック ポリシーを移行する方法を示します。 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


移行プロセスは、次の手順から構成されます。

1. [クラシック ポリシーを開き](#open-a-classic-policy)、構成設定を取得します。
2. 新しい Azure AD 条件付きアクセス ポリシーを作成し、クラシック ポリシーを置き換えます。 
3. クラシック ポリシーを無効にします。



## <a name="open-a-classic-policy"></a>クラシック ポリシーを開く

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration-mfa/01.png)

2. **[Azure Active Directory]** ページの **[管理]** セクションで、**[条件付きアクセス]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration-mfa/02.png)

3. **[管理]** セクションで、**[クラシック ポリシー (プレビュー)]** をクリックします。

    ![クラシック ポリシー](./media/active-directory-conditional-access-migration-mfa/12.png)

4. クラシック ポリシーの一覧で、クラウド アプリの**多要素認証**が必要なポリシーをクリックします。

    ![クラシック ポリシー](./media/active-directory-conditional-access-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>新しい条件付きアクセス ポリシーを作成する


1. [Azure portal](https://portal.azure.com) の左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。

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

8. **[クラウド アプリ]** ページで、次の手順を実行します。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/08.png)

    a. **[アプリを選択]** をクリックします。

    b. **[選択]** をクリックします。

    c. **[選択]** ページでクラウド アプリを選択し、**[選択]** をクリックします。

    d. **[クラウド アプリ]** ページで、**[完了]** をクリックします。



9. **[多要素認証を要求する]** を選択している場合:

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/26.png)

    a. **[アクセス制御]** セクションで、**[許可]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/27.png)

    b. **[許可]** ページで、**[アクセス権の付与]** をクリックし、**[多要素認証を要求する]** をクリックします。

    c. **[選択]** をクリックします。


10. **[オン]** をクリックしてポリシーを有効にします。

    ![条件付きアクセス](./media/active-directory-conditional-access-migration/30.png)



## <a name="disable-the-classic-policy"></a>クラシック ポリシーを無効にする

クラシック ポリシーを無効にするには、**[詳細]** ビューで **[無効]** をクリックします。

![クラシック ポリシー](./media/active-directory-conditional-access-migration-mfa/14.png)



## <a name="next-steps"></a>次の手順

- クラシック ポリシーの移行について詳しくは、「[Azure Portal でクラシック ポリシーを移行する](active-directory-conditional-access-migration.md)」をご覧ください。


- 条件付きアクセス ポリシーの構成方法を把握するには、「[Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](active-directory-conditional-access-app-based-mfa.md)」を参照してください。

- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](active-directory-conditional-access-best-practices.md)」を参照してください。 
