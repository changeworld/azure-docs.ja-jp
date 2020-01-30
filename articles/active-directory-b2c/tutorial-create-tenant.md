---
title: チュートリアル - Azure Active Directory B2C テナントを作成する
description: Azure portal を使用して Azure Active Directory B2C テナントを作成し、アプリケーションの登録の準備をする方法について説明します。
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9cb23dc11a853401d8d99a750a0c79082adbe036
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547593"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>チュートリアル:Azure Active Directory B2C テナントの作成

アプリケーションが Azure Active Directory B2C (Azure AD B2C) とやりとりできるようにするには、管理しているテナントに登録する必要があります。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク
> * Azure AD B2C テナントが含まれているディレクトリに切り替える
> * Azure portal で Azure AD B2C リソースを "**お気に入り**" として追加する

次のチュートリアルでは、アプリケーションを登録する方法を学習します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを作成する

1. [Azure portal](https://portal.azure.com/) にサインインします。 サブスクリプション内、またはサブスクリプション内のリソース グループ内で[共同作成者](../role-based-access-control/built-in-roles.md)以上のロールが割り当てられている Azure アカウントでサインインします。

1. ご利用のサブスクリプションが含まれているディレクトリを選択します。

    Azure portal のツール バーで、 **[Directory + Subscription]\(ディレクトリ + サブスクリプション\)** アイコンを選択し、ご利用のサブスクリプションが含まれているディレクトリを選択します。 このディレクトリは、Azure AD B2C テナントを含むディレクトリと異なります。

    ![サブスクリプション テナント、サブスクリプション テナントが選択された状態のディレクトリ + サブスクリプションのフィルター](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Azure portal メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。
1. **[Azure Active Directory B2C]** を検索して、 **[作成]** を選択します。
1. **[Create a new Azure AD B2C Tenant]\(新しい Azure AD B2C テナントの作成\)** を選択します。

    ![Azure portal で選択された新しい Azure AD B2C テナントを作成する](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. **[Organization name]\(組織名\)** と **[Initial domain name]\(初期ドメイン名\)** を入力します。 **[Country or region]\(国/リージョン\)** を選択し (後で変更できません)、 **[作成]** を選択します。

    ドメイン名は完全なテナント ドメイン名の一部として使用されます。 この例では、テナント名は *contosob2c.onmicrosoft.com* です。

    ![Azure portal でのサンプル値が含まれているテナントの作成フォーム](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. テナントの作成が完了したら、テナント作成ページの上部にある **[Create new B2C Tenant or Link to existing Tenant]\(新しい B2C テナントの作成または既存のテナントへのリンク\)** リンクを選択します。

    ![Azure portal で強調表示されたテナントのリンクの階層リンク](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. **[Link an existing Azure AD B2C Tenant to my Azure subscription]\(既存の Azure AD B2C テナントを Azure サブスクリプションにリンクする\)** を選択します。

   ![Azure portal での既存のサブスクリプションにリンクの選択](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. 作成した **Azure AD B2C テナント**を選択し、ご利用の**サブスクリプション**を選択します。

    **[リソース グループ]** には **[新規作成]** を選択します。 テナントを含むリソース グループの**名前**を入力し、**リソース グループの場所**を選択してから、 **[作成]** を選択します。

    ![Azure portal でのサブスクリプションのリンク設定のフォーム](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)
    
    課金のために、複数の Azure AD B2C テナントを 1 つの Azure サブスクリプションにリンクすることができます。 

## <a name="select-your-b2c-tenant-directory"></a>B2C テナント ディレクトリを選択する

新しい Azure AD B2C テナントの使用を開始するには、テナントが含まれているディレクトリに切り替える必要があります。

Azure portal の上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択してから、Azure AD B2C テナントを含むディレクトリを選択します。

新しい Azure B2C テナントが最初からリストに表示されない場合、ブラウザー ウィンドウを更新し、上部メニューでもう一度 **[ディレクトリ + サブスクリプション]** を選択します。

![Azure portal で選択された B2C テナントを含むディレクトリ](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C をお気に入りとして追加する (省略可能)

この省略可能な手順を実行すると、次のチュートリアルおよび以降のすべてのチュートリアルでの Azure AD B2C テナントの選択が容易になります。

テナントの操作が必要になるたびに **[すべてのサービス]** から「*Azure AD B2C*」を検索する代わりに、そのリソースをお気に入りとして設定することができます。 これにより、ポータル メニューの **[お気に入り]** セクションから選択することで、Azure AD B2C テナントを素早く参照することができます。

この操作は 1 回だけ実行する必要があります。 これらの手順を実行する前に、前の「[B2C テナントのディレクトリを選択する](#select-your-b2c-tenant-directory)」セクションの説明に従って Azure AD B2C テナントを含むディレクトリに切り替えていることを確認してください。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Azure portal のメニューで、 **[すべてのサービス]** を選択します。
1. **[すべてのサービス]** 検索ボックスで、「**Azure AD B2C**」を検索し、検索結果の上にマウス ポインターを移動して、ヒント内の星のアイコンを選択します。 **Azure AD B2C** が Azure portal の **[お気に入り]** に表示されるようになりました。
1. 新しいお気に入りの位置を変更する場合は、Azure portal のメニューに移動し、 **[Azure AD B2C]** を選択して、目的の位置まで上下にドラッグします。

    ![Azure AD B2C、[お気に入り] メニュー、Microsoft Azure portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>次のステップ

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントを作成する
> * サブスクリプションへのテナントのリンク
> * Azure AD B2C テナントが含まれているディレクトリに切り替える
> * Azure portal で Azure AD B2C リソースを "**お気に入り**" として追加する

次に、新しいテナントに Web アプリケーションを登録する方法を学びます。

> [!div class="nextstepaction"]
> [アプリケーションの登録 >](tutorial-register-applications.md)
