---
title: "チュートリアル: Pingboard を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs"
description: "Azure Active Directory を構成して、ユーザー アカウントを Pingboard に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。"
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
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: f74e890cf716cfd4bc7b41fcc4aa244969cafde5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>チュートリアル: Pingboard を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure Active Directory から Pingboard にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するために必要な手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント
*   Pingboard テナント ([Pro アカウント](https://pingboard.com/pricing)) 
*   Admin アクセス許可がある Pingboard のユーザー アカウント 

> [!NOTE] 
> Azure Active Directory プロビジョニング統合では、ご利用のアカウントから使用できる Pingboard API (`https://your_domain.pingboard.com/scim/v2`) が必要です。

## <a name="assigning-users-to-pingboard"></a>Pingboard へのユーザーの割り当て

Azure Active Directory では、選択されたアプリケーションへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure Active Directory 内のアプリケーションに "割り当て" 済みのユーザーのみが同期されます。 

プロビジョニング サービスを構成して有効にする前に、Pingboard アプリへのアクセスが必要なユーザーを表す Azure Active Directory 内のユーザーを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを Pingboard アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>ユーザーを Pingboard に割り当てる際の重要なヒント

*   Pingboard には、Azure Active Directory ユーザーを 1 人だけ割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後で追加のユーザーを割り当てられます。

## <a name="configuring-user-provisioning-to-pingboard"></a>Pingboard へのユーザー プロビジョニングの構成 

このセクションでは、Azure Active Directory を Pingboard のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure Active Directory のユーザーの割り当てに基づいて割り当て済みのユーザー アカウントを Pingboard で作成、更新、無効化する手順を説明します。

> [!TIP]
> Pingboard では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-active-directory"></a>Azure Active Directory で Pingboard への自動ユーザー アカウント プロビジョニングを構成する

1)  [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2) シングル サインオンのために Pingboard を既に構成している場合は、検索フィールドで Pingboard のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **Pingboard** を検索します。 検索結果から **Pingboard** を選択してアプリケーションの一覧に追加します。

3)  Pingboard のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4)  **[プロビジョニング モード]** を **[自動]** に設定します。

![Pingboard のプロビジョニング](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5) [管理者資格情報] セクションで、次の手順を実行します。

* **[テナントの URL]** ボックスに「`https://your_domain.pingboard.com/scim/v2`」と入力します。"your_domain" は、実際のドメインに置き換えてください。
* 管理者アカウントを使用して [Pingboard](https://pingboard.com/) にログインします。
* [アドオン]、[統合]、[Azure Active Directory] の順に選択します。
* [構成] タブをクリックし、**[Enable user provisioning from Azure]\(Azure からのユーザー プロビジョニングを有効にする\)** を選択します。
* **[OAuth Bearer Token]\(OAuth ベアラー トークン\)** フィールドをコピーして **[Secret Token]\(シークレット トークン\)** ボックスに入力します。

6) Azure Portal で、**[テスト接続]** をクリックして Azure Active Directory が Pingboard アプリに接続できることを確認します。 接続が失敗した場合、使用中の Pingboard アカウントに Admin アクセス許可があることを確認して、**"テスト接続"** の手順をもう一度試してください。

7) プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、次のチェック ボックスをオンにします。

8) **[Save]** をクリックします。 

9) [マッピング] セクションの **[Synchronize Azure Active Directory Users to Pingboard]\(Azure Active Directory ユーザーを Pingboard に同期する\)** を選びます。

10) **[属性マッピング]** セクションで、Azure Active Directory から Pingboard に同期されるユーザーの属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Pingboard のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。 詳細については、[ユーザー プロビジョニング属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)に関するページを参照してください。

11) Pingboard に対して Azure Active Directory プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

12) **[保存]** をクリックすると、Pingboard に割り当てられたユーザーの初期同期が開始されます。

初回は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 20 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、プロビジョニング サービスによって Pingboard アプリに対して実行されたすべてのアクションが記載されています。

Azure Active Directory プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](active-directory-saas-provisioning-reporting.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-enterprise-apps-manage-provisioning.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [シングル サインオンの構成](active-directory-saas-pingboard-tutorial.md)
