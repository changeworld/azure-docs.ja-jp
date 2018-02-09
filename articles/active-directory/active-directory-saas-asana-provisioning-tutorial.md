---
title: "チュートリアル: Asana を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs"
description: "Azure Active Directory を構成して、ユーザー アカウントを Asana に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。"
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 4f3bd11a99f43d6405ea285a7a283179d561f92a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>チュートリアル: Asana を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Asana にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Asana と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント
*   [Enterprise](https://www.asana.com/pricing) プラン以上の有効な Asana テナント 
*   Admin アクセス許可がある Asana のユーザー アカウント 

> [!NOTE] 
> Azure AD プロビジョニング統合では、Asana で使用できる [Asana API](https://app.asana.com/api/1.0/scim/Users) が必要です。

## <a name="assigning-users-to-asana"></a>Asana へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーのみが同期されます。 

プロビジョニング サービスを構成して有効にする前に、Asana アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Asana アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>ユーザーを Asana に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを Asana に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後で追加のユーザーを割り当てられます。

## <a name="configuring-user-provisioning-to-asana"></a>Asana へのユーザー プロビジョニングの構成 

このセクションでは、Azure AD を Asana のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーの割り当てに基づいて割り当て済みのユーザー アカウントを Asana で作成、更新、無効化する手順を説明します。

> [!TIP]
> Asana では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Azure AD で Asana への自動ユーザー アカウント プロビジョニングを構成するには

1)  [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2) シングル サインオンのために Asana を既に構成している場合は、検索フィールドで Asana のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Asana** を検索します。 検索結果から **Asana** を選択してアプリケーションの一覧に追加します。

3)  Asana のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4)  **[プロビジョニング モード]** を **[自動]** に設定します。

![Asana のプロビジョニング](./media/active-directory-saas-asana-provisioning-tutorial/asanaazureprovisioning.png)

5) [管理者資格情報] セクションで以下の手順に従ってトークンを生成し、**[シークレット トークン]** ボックスに入力します。

* 管理者アカウントを使用して [Asana](https://app.asana.com) にログインします。
* 上部のバーのプロフィール写真をクリックし、現在の組織名の [Settings]\(設定\) を選択します。
* [Service Accounts]\(サービス アカウント\) タブに移動します。
* [Add Service Account]\(サービス アカウントの追加\) をクリックします。
* [Name]\(名前\)、[About]\(詳細\)、プロフィール写真を必要に応じて更新し、**トークン**をコピーして、[Save Changes]\(変更の保存\) をクリックします。

6) Azure Portal で、**[テスト接続]** をクリックして Azure AD が Asana アプリに接続できることを確認します。 接続が失敗した場合、使用中の Asana アカウントに Admin アクセス許可があることを確認して、**"テスト接続"** の手順をもう一度試してください。

7) プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

8) **[Save]** をクリックします。 

9) [マッピング] セクションの **[Synchronize Azure Active Directory Users to Asana]\(Azure Active Directory ユーザーを Asana に同期する\)** を選択します。

10) **[属性マッピング]** セクションで、Azure AD から Asana に同期されるユーザー属性を確認します。 **[Matching (照合)]** プロパティとして選択されている属性は、更新処理で Asana のユーザー アカウントとの照合に使用されることに注意してください。 **[保存]** ボタンをクリックして変更をコミットします。 詳細については、[ユーザー プロビジョニング属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)に関するページを参照してください。

11) Asana に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

12) **[Save]** をクリックします。 

[ユーザー] セクションで Asana に割り当てたユーザーの初期同期が開始されます。 初回は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 20 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、プロビジョニング サービスによって Asana アプリに対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](active-directory-saas-provisioning-reporting.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-enterprise-apps-manage-provisioning.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [シングル サインオンの構成](active-directory-saas-asana-tutorial.md)
